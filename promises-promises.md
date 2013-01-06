
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

A [colleague](https://github.com/bazzel) pointed me to this video: [I .promise() to show you .when() to use Deferreds by Alex McPherson](http://www.youtube.com/watch?v=juRtEEsHI9E)

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
  products.findByArticleCode('abcd').then ->
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
great write-up about promises: [You're Missing the Point of Promises](https://gist.github.com/3889970)

It seemed that the implementation of promises done by the jQuery guys is
actually a faulty one and not compatible with the 'specs' of [Promises/A](http://wiki.commonjs.org/wiki/Promises/A)

There is a lot more to promises then meets the eye.

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
        done error

Synchronous trigger testing can be done using `chai-backbone` plugin I
wrote some time ago.

  @object.should.trigger('eventname').when ->
    @object.trigger('eventname')

But this didn't work for promises yet.


