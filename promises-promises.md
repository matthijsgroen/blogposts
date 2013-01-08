I'm developing backbone front-end apps for almost a year now. Lately the role of asynchronous objects is increasing in the projects I work on.

Imagine the following code (in coffeescript):

    class Products extends Backbone.Collection

      findByArticleCode: (code) ->
        # return a product we know
        p = @find (product) -> product.get('code') is code
        return p if p?

        # try to find product through back-end
        p = new Product { code }
        @add p
        p.fetch()
        p

This method returns an Object either populated or one in a state of flux (you never know if the fetch will be successful). To make the behavior a bit more consistent, I started to use the triggers mechanic more. I fired a trigger when I found the cached object or when the back-end request returned successfully.

The code as a consumer the started to look like this:

    products = new Products
    products.on 'fetch:product', (product) ->
      # do something with the product
    products.findByArticleCode('abcd')

This way it works in all cases, but there are some major problems:

  1. Readability. The code above is backwards. First the handling is described, before an article is fetched anyway
  2. Magic. The 'fetch:product' trigger seems to come out of nowhere, and can be triggered by other findBy... methods
  3. Error handling. I now need to trigger some 'product not found' event to do exception handling

It gets worse if you need to wrap this fetched class into another and do a request for that object as well and wait for that request to be succesful as well.

A [colleague](https://github.com/bazzel) pointed me to this video: [I .promise() to show you .when() to use Deferreds by Alex McPherson][jquery-video]

*Deferreds are exactly what I was looking for!*

the code started to change:

    findByArticleCode: (code) ->
      def = $.Deferred()
      p = @find (product) -> product.get('code') is code
      if p?
        def.resolve(p)
      else
        p = new Product { code }
        p.fetch()
        .done((product) => @add product)
        .done((product) -> def.resolve product)
      def

This way this method always returns the same. A so called 'promise'.
This code may not seem that different, but using the code is changed
dramatically:

  products = new Products
  products.findByArticleCode('abcd').then(product) ->
    # do something with the product

Advantages of this code:

  1. No 'trigger' magic between them
  2. No registering first and calling later
  3. Less administration by unregistering
  4. Then is a direct cause of the call, others can not intervene here
     (like others using same trigger)

To test code like this, I wanted to use [Chai-as-promised](https://github.com/domenic/chai-as-promised) but stubled across
the following issue: [Chai as promised is incompatible with jQuery so-called 'promises'](https://github.com/domenic/chai-as-promised/issues/12)

[Dominic Denicola](https://gist.github.com/domenic) pointed me to this
great write-up about promises: [You're Missing the Point of Promises][promises-gist]

So what are promises?
---------------------

The article above does a far better job explaining than I do, but for
sake of completeness I want to give a short explanation. For the
details, please read [You're Missing the Point of Promises][promises-gist]

The promise pattern/mechanic is basically a way to mimic a synchronous
function. A synchonous function is basically something that accepts
arguments, and returns a value/object. When something goes wrong in the
returning of this function an exception is thrown.

    synchronousMethod: (arg) ->
      if arg > 10
        throw new Error "Arg can't be larger than 10!"
      else
        return arg + 10

In asynchronous code, when the method is executed, we don't know its
return value yet. And we also don't know already if the method will
eventually fail.

In the past this problem was solved by providing callbacks. Mostly a
callback if stuff went well, and one when stuff went wrong.

Promises are a way to streamline this behaviour.

You basically call a method (for example: `fetchObjectFromServer`) and
you get a promise back. This promise claims to have eventually fetched
an object from a remote server. So a promise also has a return value
(namely the fetched object). A callback can be bound to this promise to
retrieve this return value when it is available. To handle errors, a
callback can also be attached to do the error handling.

It seemed that the implementation of promises done by the jQuery guys is
actually a faulty one and not compatible with the 'specs' of [Promises/A][promises-spec]

There is a lot more to promises then meets the eye.
Mainly in the case that each part of the callback chain is also a
promise. Again, the gist post is better in explaining than I am :-)

The Promises/A page also has a lists of valid implementations, and I
switched to '[when](https://github.com/cujojs/when)' (tried
'[Q](https://github.com/kriskowal/q)') but 'when' seems to have faster
performance (test times where lower).

The changed code now looks like this:

    findByArticleCode: (code) ->
      def = window.when.defer()
      p = @find (product) -> product.get('code') is code
      if p?
        def.resolve(p)
      else
        p = new Product { code }
        p.fetch()
        .done((product) => @add product)
        .done((product) -> def.resolve product)
      def

So only the second line has changed. But now you can as a consumer of
the promise do better chaining and error handling.

Testing promises
----------------

The test code for this now also looks awesome: (mocha / chai)

  beforeEach ->
    @product = new Product code: 'abcd'
    @products = new Products [@product]

  describe '.findByArticleCode', ->
    it 'fetches already known products', (done) ->
      @products.findByArticleCode('abcd').should.become(@product).and.notify(done)

The `.notify(done)` part is needed for the asynchronity of the test.
Hopefully I will be ably to plug `mocha-as-promised` into Konacha soon
so that it is no longer necessary anymore.

Since I also like to fire triggers and test if that is happening tests
get a bit mode cumbersome again:

    it 'lists the new page as active when fetched', (done) ->
      spy = sinon.spy()
      @pages.on 'change:active', spy

      @router.showPage('other/page').then =>
        spy.should.have.been.calledWith(@nextPage, yes)
        done()
      , (error) ->
        done new Error error

Synchronous trigger testing can be done using `chai-backbone` plugin I
wrote some time ago.

    @object.should.trigger('eventname').when ->
      @object.trigger('eventname')

But this didn't work for promises yet.

I have updated the `chai-changes` plugin that promises are supported as return value of the method provided in `when`.

The 'when' block can return a promise now, and the post-conditions are run when the promise is fulfilled.
The promise returned is a promise about the expectations, and not the
promise returned by the callback. So if the callback returns a promise
that is resolved, but the expectations after that resolved promise fail,
the 'when' method returns a rejected promise with the assertion error as
argument.

When using the 'mocha-as-promised' extension, the code can then be
cleaned up to:

    it 'lists the new page as active when fetched', ->
      @pages.should.trigger('change:active', with: [@nextPage, yes]).when => @router.showPage('other/page')

Which is considerable less code then above but also more readable.

I use Konacha to test my code, and it runs the test in a browser (or
headless browser using the `konacha:run` rake task). Unfortunately,
I am not yet able to get the mocha-as-promised working with Konacha, so
I needed to fall back to using the 'notify' chaining method:

    it 'lists the new page as active when fetched', (done) ->
      @pages.should.trigger('change:active', with: [@nextPage, yes]).when(=> @router.showPage('other/page')).notify(done)

Promises are really cool to use to handle the asynchronous aspects of
your front-end logic, and using chai, chai-as-promised and now also the
chai-changes it is really good to test these kind of processes!

[jquery-video]: http://www.youtube.com/watch?v=juRtEEsHI9E
  "I .promise() to show you .when() to use Deferreds by Alex McPherson"
[promises-gist]: https://gist.github.com/3889970
  "You're Missing the Point of Promises"
[promises-spec]: http://wiki.commonjs.org/wiki/Promises/A
  "Promises/A"

