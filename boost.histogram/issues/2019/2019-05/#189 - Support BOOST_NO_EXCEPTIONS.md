# #189 - Support BOOST_NO_EXCEPTIONS [Closed]

> Username: glenfe  
> Created at: 2019-05-12 15:44:06 UTC  
> Updated at: 2019-05-13 11:29:56 UTC  
> Closed at: 2019-05-13 11:29:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/189  

The library does this well in most places, but two still use `try`, `catch`, `throw` directly which won't compile on those environments where exception support is disabled.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-05-12 18:33:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491618416  

Working on it... https://github.com/HDembinski/histogram/pull/32

---

## Comment 2

> Username: glenfe  
> Created at: 2019-05-12 18:41:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491618970  

Your tests can continue to `throw`, i.e. only the library needs to support `BOOST_NO_EXCEPTIONS` (tests that test exceptions should test exceptions, to be able to do things like `BOOST_TEST_THROWS`).  
  
In general I avoid `try`, `catch`, re-`throw` and RAII instead, since that works equally in exceptions mode and without exceptions: https://github.com/boostorg/histogram/pull/190  
  
The PR also switches to `boost::alloc_construct_n` and `boost::alloc_destroy_n` from Boost.Core.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-05-12 20:14:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491625684  

I didn't see your PR in time, which is of course much nicer than my solution. You said I can throw/catch in the tests, but I also want to test the "-fno-exceptions -fno-rtti" support, and then I can't throw/catch in the tests either. I will try to merge the two.

---

## Comment 4

> Username: glenfe  
> Created at: 2019-05-12 20:20:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491626059  

> You said I can throw/catch in the tests, but I also want to test the "-fno-exceptions -fno-rtti" support, and then I can't throw/catch in the tests either.   
  
*Nod*, if you want to test the -fno-exceptions support, then you can't throw in the tests that test it.  
  
Just make sure you don't sacrifice any existing testing you have of exceptions support. e.g. If you have tests that check that an exception was correctly thrown via `BOOST_TEST_THROWS()`, those tests are valuable too.

---

## Comment 5

> Username: glenfe  
> Created at: 2019-05-12 20:30:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491626818  

> I will try to merge the two.  
  
#190 updated (should merge cleanly).

---

## Comment 6

> Username: HDembinski  
> Created at: 2019-05-13 10:36:01 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491769529  

> If you have tests that check that an exception was correctly thrown via BOOST_TEST_THROWS(), those tests are valuable too.  
  
Sure, I just put ifdefs around those. I want to keep 100 % line coverage and that means I have to test various exception paths as well.

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-05-13 10:37:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/189#issuecomment-491769895  

Thank you again for this nice patch. I am aware of the RAII method, but didn't think of using it here. Very nice, I learned something.
