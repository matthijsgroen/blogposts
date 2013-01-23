Ode to open source
==================

Since half a year I changed my development environment from a graphical
IDE to a tmux and vim environment. I stopped using my mouse and am now a
keyboard centric computer user. I find myself more productive this way.

Nowadays I use teamocil to manage my tmux configuration for each project
I work on, which I check in in the VCS so that the whole team is
basically using the same setup. We pair programming local and remotely,
and since we share the environment using tmux we only have the terminal
to be our IDE. This not only makes it useable and fast on instable
connections, is is a far superior experience compared to screensharing
which has to do aweful compression.

Since I am a webdeveloper and building mostly front-end apps in
javascript/coffeescript nowadays, it is weird to actually not use the
browser directly. I write my code test-driven, so my specs prove to me
that the application works. Only when a feature is indicated by the
tests to be complete or CSS styling needs to be done I open the browser.

So in my quest to do everything in my development process in tmux, I had
some things I wanted to be better.

For instance, I use [Guard][] to automatically run the specs when I
store a spec or source file. There are cool Growl notifications
available, but when using tmux over a remote connection you don't see
those. So I forked the Guard project, started hacking and made
[my first pull-request](https://github.com/guard/guard/pull/333). I
didn't know how pull requests truly worked so
[I kept adding issues](https://github.com/guard/guard/pull/334).

The cool thing was, I got feedback, tips and the stuff was merged back
in the official version which got released almost instantly in a matter
of hours.

The speed on Github is really incredible. You fork, you hack, you submit
a pull-request. Others comment, review, merge and publish.

Since then I have been pimping my environment more and more by releasing
own tools for development, or improving existing tools.

Own stuff [published on node](https://npmjs.org/~thaisi):

 - *chai-backbone*: chai matchers to assert certain Backbone.js core
   principles like triggers, routes and view events.
 - *chai-changes*: chai matchers to assert changes (inspired by
   [Rspec](http://rspec.rubyforge.org/rspec/1.1.9/classes/Spec/Matchers.html#M000428))
 - *js-factories*: Factories for a javascript environment, framework
   agnostic, inspired by [Factory-girl](https://github.com/thoughtbot/factory_girl)

Since I use the JS front-ends in combination with a Rails back-end, I
use Konacha to run these tests in an environment using capybara-webkit
as a headless browser. Node packages and ruby-gems are 2 different
package management systems and unfortunately don't mix.

This way a lot of node packages that are also useful for the rails
community end up being ported as Gem. But to create a gem for each
matcher library isn't that efficient, from a maintenance perspective,
but also from a development perspective where you need to recheck if
for each chai-js plugin a gem is available.

So I ended up making
[Konacha-chai-matchers](https://github.com/matthijsgroen/konacha-chai-matchers),
a gem meta package containing all chai-js plugins as git submodules.

To automatically run the konacha specs I use the Guard-Konacha plugin.
The problem was that this gem would kick of a rake task, which started a
server and a capybara session, ran the tests of a specific file and
exited again.

The plugin worked, but we had to wait 7-8 seconds before we could see
the fail/pass state.

So I went to do the fork-hack-submit cycle again and again the changes
to speed up where both merged back into konacha and guard-konacha in a
matter of hours.

So now I can run speedy tests, get a nice notification due to my own
contributions. It feels really good to give back to such a great
open-source community where we all benefit from.

The Only gripe I had was pivotal tracker. Pivotal tracker is a great
tool in its own right, but really lacks keyboard support. Fortunately
there was already a greate command-line version available.

But as you can guess already, it had some limitations. And again, my
extensions where added in a matter of hours.

This way of working is really addictive, and I really hope that I can contribute to more projects soon!

The Github community is great, active, generous, great in discussions
and review, and thankful.


