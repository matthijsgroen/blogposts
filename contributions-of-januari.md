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

# TODO: Add additions to own repos
Chai-backbone


Commits
-------
Pushed 10 commits to matthijsgroen/chai-backbone Jan 06 - Jan 28
Pushed 26 commits to matthijsgroen/konacha-chai-matchers Jan 06 - Jan 28
Pushed 3 commits to matthijsgroen/js-factories Jan 28
Pushed 21 commits to matthijsgroen/chai-changes Jan 06 - Jan 28
Pushed 17 commits to kabisaict/yaata Jan 10 - Jan 24
Pushed 13 commits to matthijsgroen/blogposts Jan 06 - Jan 23
Pushed 1 commit to matthijsgroen/person-name Jan 06

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
[mapp-8]: https://github.com/domenic/mocha-as-promised/issues/8 "beforeEach and afterEach time out almost every time"
[mapp-9]: https://github.com/domenic/mocha-as-promised/pull/9 "Show original function code for toString()"
[mapp-10]: https://github.com/domenic/mocha-as-promised/pull/10 "Return false if function is not wrapped somehow."

