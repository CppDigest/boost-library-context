# #607 Literal test simplification [Merged]

> Username: jagerman  
> Created at: 2016-05-02 23:40:25 UTC  
> Updated at: 2018-06-05 02:20:30 UTC  
> Merged at: 2016-05-04 02:31:32 UTC  
> Closed at: 2016-05-04 02:31:32 UTC  
> Url: https://github.com/boostorg/compute/pull/607  

The tests are failing under OS X/clang, but it isn't obvious to me why, or exactly which part is failing.  This commit simplifies the test code by ignoring the stringstream I/O status, and reorganizes it to be clearer whether it's the initial conversion to string that is failing, or the subsequent conversion back from string.  
  
(This may or may not clear up the OSX/clang failure, but it's better test code anyway).  
  
This also incorporates the PR #603 fix.

---

## Comment 1

> Username: jagerman  
> Created_at: 2016-05-03 00:54:10 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216407434  

This did not fix the OSX/clang error ([link](https://travis-ci.org/boostorg/compute/jobs/127370548#L1769)), and I'm not sure why.  
  
The conversion to float is going okay, because the first test:  
  
`BOOST_CHECK_EQUAL(literal.substr(0, 6), "1.2345");`  
  
is not failing--so we have something starting with 1.2345, as desired, and it's probably the right value.  But then, from the test output, this code:  
  
```  
        std::istringstream iss(literal);  
        float x;  
        std::string suffix;  
        iss >> x >> suffix;  
```  
  
seems to be completely broken: we end up with x == 0.f, and suffix (which should be "f") is empty.  Am I doing something wrong in the std::istringstream use here?

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-05-03 00:58:37 UTC  
> Updated_at: 2016-05-03 01:11:07 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216408000  

[![Coverage Status](https://coveralls.io/builds/6020952/badge)](https://coveralls.io/builds/6020952)  
  
Coverage increased (+0.003%) to 88.845% when pulling **210668843cea6ef06f9c029b514d2eefeab52b87 on jagerman:literal-simplify-test** into **62d487c0368892cece0554a7e769781bebd66acb on boostorg:develop**.

---

## Comment 3

> Username: jagerman  
> Created_at: 2016-05-03 02:28:44 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216420297  

[The bug is in libc++](https://llvm.org/bugs/show_bug.cgi?id=17782), and shows up with the clang++/libc++ combination on linux, too: libc++ fails if asked to extract float/double values followed various special letters, of which "f" is one.  
  
The cce140e commit I just pushed works around the problem, which should resolve the test failure.

---

## Comment 4

> Username: coveralls  
> Created_at: 2016-05-03 03:28:55 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216427196  

[![Coverage Status](https://coveralls.io/builds/6022788/badge)](https://coveralls.io/builds/6022788)  
  
Coverage increased (+0.002%) to 88.844% when pulling **cce140e633eee45270812a54dfaf0080d017c60d on jagerman:literal-simplify-test** into **62d487c0368892cece0554a7e769781bebd66acb on boostorg:develop**.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2016-05-03 08:21:01 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216465844  

OK, I also did some testing after I'd noticed those errors and it looked like there's a bug in apple clang/libc++ but somehow I couldn't find reference to the bug. Thanks!  
  
Fortunately, this bug should not effect Boost.Compute. https://github.com/boostorg/compute/pull/603 was merged so you can rebase your branch on the `develop` if you prefer.

---

## Comment 6

> Username: jagerman  
> Created_at: 2016-05-03 14:30:04 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216547071  

Just rebased the branch onto develop.  
  
Indeed the bug is only in the test program, not the actual literal generation, but eliminating the false positive test failure under clang/libc++ (not just on OS X) seems worthwhile, if minor.

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-05-03 15:37:20 UTC  
> Updated_at: 2018-06-05 02:20:30 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216569299  

[![Coverage Status](https://coveralls.io/builds/6029785/badge)](https://coveralls.io/builds/6029785)  
  
Coverage increased (+0.002%) to 88.851% when pulling **9b2c65fee992137712f9dd662c2107a7c81112d5 on jagerman:literal-simplify-test** into **51afcd0c22e85c4f764de77fcdbd1ee834c02025 on boostorg:develop**.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2016-05-04 02:31:35 UTC  
> Url: https://github.com/boostorg/compute/pull/607#issuecomment-216726112  

Merged! Thanks!

---
