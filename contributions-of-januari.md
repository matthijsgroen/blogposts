Contributions of January
========================

One of my newyear's resolutions was to do more open-source
contributions.

[Guard-Konacha][gk]
-------------

I hoped to increase the performance of this nice little gem by keeping
the capybara-session alive for multiple test runs, instead of having it
firing `rake konacha:run` every time.

[Made a pull request to do this][gkp-7]. The result was great, fast but
not without issues. One was
[it crashed when a source file was saved that didn't had a matching spec file][gki-8].
I was able to [fix this one][gkp-9] later.

Another issue was caching problems of the living session, but I
[improved that one aswell][gkp-11].

In total, Pushed 12 commits to [alexgb/guard-konacha][gk] in Jan 19 - Jan 27

[Konacha][k]
-------

[Some extensions][kp-95] to Konacha had to be made to implement the run strategy
of Guard-Konacha.

In total, Pushed 6 commits to [jfirebaugh/konacha][k] in Jan 19 - Jan 20

[PT][pt]
--

PT is a Pivotal Tracker command line interface. Since I work in Tmux
all day it is a nice addition to the terminal based workflow. It also
helps when talking with your remote pair about story tasks and he can
see them aswell.

For this gem I [added story task handling][ptp-44] and a command to see
all [started stories of the project][ptp-45].

In total, Pushed 5 commits to [raul/pt][pt] on Jan 22

[Chai-as-promised][cap]
----------------

As [you know][promises-post] I started to work a lot with promises for my async
processes lately. Naturally, I am also working with test libraries to
test those. Chai-as-promise is an assertion library extension.

It had an [issue][capi-13] that some tests failed when ran on the
command line, but passed when ran in the browser.

I figured out what [the root-cause was and proposed a fix][capp-16].

In total, Pushed 2 commits to [domenic/chai-as-promised][cap] on Jan 11

[Mocha-as-promised][map]
-----------------

Mocha is an extension/hack for the [mocha][mocha] test framework so that
it will automatically do the correct handling of tests that return a
promise. First, [I couldn't get this hack to work in the konacha environment][ki-91].
Therefor, konacha had to [upgrade their bundled mocha version][ki-92].

When trying it out in the browser, I noticed that show source would no
reveal the wrapped version of the test, and not the original code.
[Made a pull-request to fix this][mapp-9].

When I started to use this nice addition more, I got [weird timeouts][mapi-8].
Sometime later I figured out the cause and [made a fix][mapp-10] aswell.

In total, Pushed 3 commits to [domenic/mocha-as-promised][map] in Jan 09 - Jan 27

Work on own libraries
=====================

I did not only work on libraries maintained by others, I worked on some
of my own libraries aswell.

[Chai-backbone][cb]
-------------

Made the following additions to this [chai-plugin][cb-plugin]:

  * [Fixed assertion of trigger if initiated multiple times][cbc1]
  * Merged a pull request made by [Stepan Mazurov](https://github.com/smazurov),
    that changed the [spy used in testing by a stub][cbp-2]
  * Made sure [the tests now run using Node.js][cbc2], making development
    easier and testing faster.

[Released 0.8.4 to npm][cb-npm], Pushed 10 commits to
[matthijsgroen/chai-backbone][cb] Jan 06 - Jan 28

[Chai-changes][cc]
------------

Made the following additions to this [chai-plugin][cc-plugin]:

  * Made the `when` matcher that runs all before and after conditions wait
    for promises to reach their end state (reject / fulfilled)
    [fda6dc4d][ccc1] [ddf47283][ccc2]
  * Finally decided that after the `when` statement chaining should end
    and a full promise should be returned instead an assertion. This would
    make this mechanism compatible for [Mocha-as-promised][map]
    [21d2bbf2][ccc3]
  * Added a dirty hack to allow testing of Ember.js Models. They return
    a promises that somehow is unfullfilled and make the tests timeout.
    [0acfb871][ccc4]
  * Made sure [the tests now run using Node.js][ccc5], making development
    easier and testing faster.

[Released 1.2.0 to npm][cc-npm], Pushed 21 commits to
[matthijsgroen/chai-changes][cc] Jan 06 - Jan 28

[js-factories][jsf]
------------

Made the following addtions:

  * This library is now a fully compliant [AMD module][jsfc1]
  * Made also sure the tests run using Node.js

[Released 0.9.0 to npm][jsf-npm],
Pushed 3 commits to [matthijsgroen/js-factories][jsf] Jan 28

[Konacha-chai-matchers][kcm]
---------------------

Did a lot of republishing the gem, to update all dependent libraries an
changes. The most notable changes are:

  * [Added Mocha-as-promised to the collection][kcmc1]
  * [Added Chai-Things to the collection][kcmc2]
  * [Added Chai-Fuzzy to the collection][kcmc3]

[Released 0.0.14 as ruby-gem][kcm-gem],
Pushed 26 commits to [matthijsgroen/konacha-chai-matchers][kcm] Jan 06 - Jan 28


Well, Januari was quite a month :-) I don't think February can top this,
but you never know!


[promises-post]: http://matthijsgroen.posterous.com/promises-promises
[mocha]: http://visionmedia.github.com/mocha/

[gk]: https://github.com/alexgb/guard-konacha
[gkp-7]: https://github.com/alexgb/guard-konacha/pull/7 "Rewrite Guard-Konacha to increase performance"
[gki-8]: https://github.com/alexgb/guard-konacha/issues/8 "Capybara session crashes if no spec file linked to javascript file"
[gkp-9]: https://github.com/alexgb/guard-konacha/pull/9 "Check spec existence. Fixes #8"
[gkp-11]: https://github.com/alexgb/guard-konacha/pull/11 "Prevent caching results"

[k]: https://github.com/jfirebaugh/konacha
[ki-91]: https://github.com/jfirebaugh/konacha/issues/91 "How to setup mocha-as-promised?"
[ki-92]: https://github.com/jfirebaugh/konacha/issues/92 "Update to Mocha 1.8.0"
[kp-95]: https://github.com/jfirebaugh/konacha/pull/95 "Allow tight integration for Guard-Konacha"

[pt]: https://github.com/raul/pt
[ptp-44]: https://github.com/raul/pt/pull/44 "Add Story Task management"
[ptp-45]: https://github.com/raul/pt/pull/45 "Show all started stories"

[cap]: https://github.com/matthijsgroen/chai-as-promised
[capi-13]: https://github.com/domenic/chai-as-promised/issues/13 "rejected not available in konacha command line?"
[capp-16]: https://github.com/domenic/chai-as-promised/pull/16 "Change with keyword to fix #13"

[map]: https://github.com/domenic/mocha-as-promised
[mapi-8]: https://github.com/domenic/mocha-as-promised/issues/8 "beforeEach and afterEach time out almost every time"
[mapp-9]: https://github.com/domenic/mocha-as-promised/pull/9 "Show original function code for toString()"
[mapp-10]: https://github.com/domenic/mocha-as-promised/pull/10 "Return false if function is not wrapped somehow."

[cb]: https://github.com/matthijsgroen/chai-backbone
[cb-plugin]: http://chaijs.com/plugins/chai-backbone
[cb-npm]: https://npmjs.org/package/chai-backbone "Backbone assertions for the Chai assertion library"
[cbc1]: https://github.com/matthijsgroen/chai-backbone/commit/437fd6d94ad5417d5f14d476395660f1d755876e "Assert true if trigger is called multiple times"
[cbp-2]: https://github.com/matthijsgroen/chai-backbone/pull/2 "Consider using stub instead of spy"
[cbc2]: https://github.com/matthijsgroen/chai-backbone/commit/ea5aafc6b984871405f19759d19f601418205a34 "Run tests using nodejs"

[cc]: https://github.com/matthijsgroen/chai-changes
[cc-plugin]: http://chaijs.com/plugins/chai-changes
[cc-npm]: https://npmjs.org/package/chai-changes "Change assertions for the Chai assertion library"
[ccc1]: https://github.com/matthijsgroen/chai-changes/commit/fda6dc4de0204721759bc9040f45f9858a742dcb "Add support for promises in when clause"
[ccc2]: https://github.com/matthijsgroen/chai-changes/commit/ddf47283dbeeabeb40840b5e49c96c6516c0bdb1 "Validate conditions after promise fulfillment"
[ccc3]: https://github.com/matthijsgroen/chai-changes/commit/21d2bbf24af5ee8ce4a8de38a7f7507f5319250d "Change returning promise of When"
[ccc4]: https://github.com/matthijsgroen/chai-changes/commit/0acfb871bc8717d32e638f29ccd1b46ce356a6e6 "Ignore the promise of Ember models for now"
[ccc5]: https://github.com/matthijsgroen/chai-changes/commit/9af4f77b3bac03ab9b693f8abd1dc3196633df4e "Test library through nodejs"

[jsf]: https://github.com/matthijsgroen/js-factories
[jsf-npm]: https://npmjs.org/package/js-factories
[jsfc1]: https://github.com/matthijsgroen/js-factories/commit/46b6eee603309c4e5ee0ceac62d7c0f142a5269f "Create AMD Module"

[kcm]: https://github.com/matthijsgroen/konacha-chai-matchers
[kcm-gem]: https://rubygems.org/gems/konacha-chai-matchers
[kcmc1]: https://github.com/matthijsgroen/konacha-chai-matchers/commit/7b0a93be3f35f2c727aaf8bbbe61018621f4ca15 "Include mocha-as-promised"
[kcmc2]: https://github.com/matthijsgroen/konacha-chai-matchers/commit/d9c6d5f32bfd1963ca23c95f10fad42a19d8bb02 "add Chai-things to bundled plugins"
[kcmc3]: https://github.com/matthijsgroen/konacha-chai-matchers/commit/767f0c0e67d9a7ab37e44cefda961f7a4ed5299d "add Chai-Fuzzy to bundled plugins"



