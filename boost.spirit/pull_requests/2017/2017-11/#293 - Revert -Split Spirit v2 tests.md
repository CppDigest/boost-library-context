# #293 Revert "Split Spirit v2 tests" [Merged]

> Username: Kojoley  
> Created at: 2017-11-26 11:53:10 UTC  
> Updated at: 2017-11-27 11:24:23 UTC  
> Merged at: 2017-11-26 11:53:18 UTC  
> Closed at: 2017-11-26 11:53:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/293  

Reverts boostorg/spirit#290  
  
Something strange happens to `process_jam_log` on boost test runners, they are missing information for tests and them do not appear on tests matrix.  
  
I have looked at their logs and found that `revision`, `test-type` and `test-program` are empty:  
`<test-log library="spirit" revision="" test-name="karma_regression_center_alignment" test-type="" test-program="" target-directory="boost/bin.v2/libs/spirit/test/karma/karma_regression_center_alignment.test/gcc-gnu-7.1.0/debug/threadapi-pthread" toolset="gcc-gnu-7.1.0" show-run-output="true">`  
  
For me `run.py` generates a good one (and it shows nicely on regression matrix):  
`<test-log library="spirit" revision="8e95b7" test-name="karma_regression_center_alignment" test-type="run" test-program="libs/spirit/test/karma/regression_center_alignment.cpp" target-directory="boost/bin.v2/libs/spirit/test/karma/karma_regression_center_alignment.test/msvc-14.1/debug/threadapi-win32/threading-multi" toolset="msvc-14.1" show-run-output="false">`  
  
I do not understand what happens. The boost test system is really makes me very very sad.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-11-26 14:50:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347013665  

Well, somehow if I run `b2 status/ -n -a -d0 --dump-tests --limit-tests=spirit` it show all the spirit tests, while `b2 status/ -n -a -d0 --dump-tests` does not (the ones that come from `build-project`). It blows my mind.  
  
Interesting thing https://svn.boost.org/trac10/ticket/10288

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-11-26 17:39:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347025309  

Okay, Pool library is the cause, `b2 -n -a -d0 --exclude-tests=pool --dump-tests` or removing `libs/pool/test/Jamfile.v2` solves the problem. I digged down to their `use-valgrind`, but I do not understand what is going on.

---

## Comment 3

> Username: fhein  
> Created_at: 2017-11-26 18:17:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347027669  

@Kojoley : Off topic,  but I would like to express my great respect and recognition for all the work you are  currently doing to boost Spirit. That's great, awesome!  :)

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-11-26 20:21:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347035500  

It looks like I found the roots of the problem, waiting for boostorg/pool#18 to redeploy the reverted PR.

---

## Comment 5

> Username: Kojoley  
> Created_at: 2017-11-26 22:12:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347042760  

@fhein thanks for appreciation!

---

## Comment 6

> Username: octopus-prime  
> Created_at: 2017-11-27 11:24:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/293#issuecomment-347154064  

yes, i see :-|

---
