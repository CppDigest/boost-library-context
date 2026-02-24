# #249 - Karma: alternative is broken due to new `boost::optional` [Closed]

> Username: pdimov  
> Created at: 2017-10-13 00:06:57 UTC  
> Updated at: 2017-11-23 14:13:38 UTC  
> Closed at: 2017-11-13 14:21:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/249  

See f.ex. here:  
  
https://travis-ci.org/boostorg/boost/jobs/285871194  
https://travis-ci.org/boostorg/boost/jobs/285871195  
  
Apologies if you didn't want issues enabled.

---

## Comment 1

> Username: hkaiser  
> Created at: 2017-10-13 13:38:58 UTC  
> Updated at: 2017-10-13 13:40:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-336454983  

Peter,  
  
I think those are supposed to fail. Can we mark them as EXPECT_FAIL or somesuch?  
   
  
From: Peter Dimov [mailto:notifications@github.com]   
Sent: Thursday, October 12, 2017 7:07 PM  
To: boostorg/spirit <spirit@noreply.github.com>  
Cc: Subscribed <subscribed@noreply.github.com>  
Subject: [boostorg/spirit] karma_alternative1-p3, karma_alternative2-p3 fail on gcc 4.8/clang 3.9 (#249)  
  
   
  
See f.ex. here:  
  
https://travis-ci.org/boostorg/boost/jobs/285871194  
https://travis-ci.org/boostorg/boost/jobs/285871195  
  
Apologies if you didn't want issues enabled.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub <https://github.com/boostorg/spirit/issues/249> , or mute the thread <https://github.com/notifications/unsubscribe-auth/AAiq_6F1LTJVH7rtJatfpLi-ozWSzSsgks5srqmhgaJpZM4P3zH2> .  <https://github.com/notifications/beacon/AAiq_7xP_fyQprzwr48zmIUl4lRF1G27ks5srqmhgaJpZM4P3zH2.gif>

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-13 13:48:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-336457359  

When you say supposed to fail, do you mean that they should always fail, or that they are known to fail on these compilers?

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-13 13:52:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-336458519  

Looking at http://www.boost.org/development/tests/develop/developer/spirit.html, these tests do indeed fail across the board; but looking at the tests themselves, they don't seem like they are supposed to fail.

---

## Comment 4

> Username: hkaiser  
> Created at: 2017-10-13 15:51:01 UTC  
> Updated at: 2017-10-13 15:51:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-336492116  

> When you say supposed to fail, do you mean that they should always fail, or that they are known to fail on these compilers?  
  
These tests have to fail compilation in order to pass the test.  
  
> Looking at http://www.boost.org/development/tests/develop/developer/spirit.html, these tests do indeed fail across the board; but looking at the tests themselves, they don't seem like they are supposed to fail.  
  
The name indicates this (`grammar_fail`, `rule_fail`).

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-13 16:09:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-336497086  

I see no `grammar_fail` or `rule_fail` anywhere in  
  
https://github.com/boostorg/spirit/blob/develop/test/karma/alternative1.cpp  
  
and  
  
https://github.com/boostorg/spirit/blob/develop/test/karma/alternative2.cpp

---

## Comment 6

> Username: Kojoley  
> Created at: 2017-11-09 20:18:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-343278945  

@pdimov is right about the fails. Karma alternative is partly broken for about a year, the new `boost:optional` implementation had opened a can of worms. You can workaround this with `BOOST_OPTIONAL_CONFIG_USE_OLD_IMPLEMENTATION_OF_OPTIONAL`. I am currently investigating the issue.  
```  
testing.capture-output bin.v2/libs/spirit/test/karma_alternative1-p3.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/karma_alternative1-p3.run  
====== BEGIN OUTPUT ======  
in test: result is false  
in test: result is false  
in test: result is false  
libs/spirit/test/karma/alternative1.cpp(67): test 'test("10", char_ | int_, v)' failed in function 'int main()'  
in test: result is false  
libs/spirit/test/karma/alternative1.cpp(68): test 'test("10", int_ | char_, v)' failed in function 'int main()'  
in test: generated "a"  
libs/spirit/test/karma/alternative1.cpp(71): test 'test("10", int_ | lit('a') | char_, v)' failed in function 'int main()'  
in test: result is false  
libs/spirit/test/karma/alternative1.cpp(74): test 'test("c", char_ | int_, v)' failed in function 'int main()'  
in test: generated "a"  
libs/spirit/test/karma/alternative1.cpp(76): test 'test("c", char_ | lit('a') | int_, v)' failed in function 'int main()'  
in test: generated "a"  
libs/spirit/test/karma/alternative1.cpp(78): test 'test("c", int_ | char_ | lit('a'), v)' failed in function 'int main()'  
in test: result is false  
6 errors detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
```  
testing.capture-output bin.v2/libs/spirit/test/karma_alternative2-p3.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/karma_alternative2-p3.run  
====== BEGIN OUTPUT ======  
in test: result is false  
in test: result is false  
in test: result is false  
in test: result is false  
in test_delimited: result is false  
in test_delimited: result is false  
in test_delimited: generated "11 "  
libs/spirit/test/karma/alternative2.cpp(119): test 'test_delimited("10.0 ", double_ | lit(11), v, char_(' '))' failed in function 'int main()'  
in test_delimited: result is false  
in test_delimited: result is false  
1 error detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 7

> Username: hkaiser  
> Created at: 2017-11-13 15:29:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-343954686  

@Kojoley thanks a lot! Your help is very much appreciated!

---

## Comment 8

> Username: pdimov  
> Created at: 2017-11-13 16:20:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-343971980  

I confirm that Travis is happy with this change: https://travis-ci.org/boostorg/boost/builds/301455130

---

## Comment 9

> Username: pdimov  
> Created at: 2017-11-13 17:04:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-343986495  

Appveyor also fine: https://ci.appveyor.com/project/boostorg/boost/build/1.0.4754-develop  
  
Except for msvc-12.0 errors caused by https://github.com/boostorg/fusion/issues/159

---

## Comment 10

> Username: Flast  
> Created at: 2017-11-23 14:13:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/249#issuecomment-346628842  

I believe boostorg/fusion#161 fixes the error.
