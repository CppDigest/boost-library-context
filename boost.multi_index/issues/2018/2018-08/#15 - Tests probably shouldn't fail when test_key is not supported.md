# #15 - Tests probably shouldn't fail when test_key is not supported [Closed]

> Username: pdimov  
> Created at: 2018-08-29 19:21:16 UTC  
> Updated at: 2018-09-03 07:56:04 UTC  
> Closed at: 2018-09-03 07:56:04 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15  

```  
testing.capture-output bin.v2/libs/multi_index/test/test_key.test/gcc-4.8/debug/cxxstd-11-iso/test_key.run  
====== BEGIN OUTPUT ======  
libs/multi_index/test/test_key.cpp(22): test 'false' failed in function 'void test_key()'  
1 error detected.  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
It would probably be better to use something like  
  
```  
#include <boost/config/pragma_message.hpp>  
  
#if !defined(BOOST_MULTI_INDEX_KEY_SUPPORTED)  
  
BOOST_PRAGMA_MESSAGE("Skipping test, boost::multi_index::key not supported")  
  
void test_key()  
{  
}  
  
#else  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2018-08-29 21:15:54 UTC  
> Updated at: 2018-08-29 21:16:25 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15#issuecomment-417108566  

Hi Peter,  
  
Thanks for the pointer! I'd indeed like to generate some warning rather than brutally fail, and had this in my to-investigate list. The problem I see with `BOOST_PRAGMA_MESSAGE` is that in many environments the test matrix shows green without attached messages, as evidenced in `pragma_message_test`.  Are you planning on improving this situation? I guess I'm looking for a `BOOST_PRAGMA_WARNING` of sorts.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-08-29 21:48:34 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15#issuecomment-417117514  

I don't think it's possible. The best that comes to mind is  
  
```  
#ifdef __GNUC__  
# warning Skipping test, boost::multi_index::key not supported  
#else  
BOOST_PRAGMA_MESSAGE("Skipping test, boost::multi_index::key not supported")  
#endif  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2018-08-29 21:56:15 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15#issuecomment-417119537  

I see that you don't use Travis/Appveyor so it's probably not a big deal to just fail, but if you start, you'll see that it's much more convenient to let the test pass and keep it green. These integrate with Github and run on pull requests, so you can immediately estimate the quality of the submission.  
  
I've rigged the Travis for boostorg/boost to run the tests on the library that's been updated, which is how I saw the above failure.  
  
https://travis-ci.org/boostorg/boost/builds  
https://travis-ci.org/boostorg/boost/builds/422173681  
  
If you decide to set up Travis, you can look at the .travis.yml files for System or SmartPtr or Core or any of my libraries, they're fairly straightforward.

---

## Comment 4

> Username: joaquintides  
> Created at: 2018-08-30 09:30:42 UTC  
> Updated at: 2018-08-30 09:31:08 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15#issuecomment-417254372  

I've just done as you suggested (https://github.com/boostorg/multi_index/commit/e86162cff82cea02d0f7482611081d488a90e305).  
  
As for Travis/AppVeyor support, I already included it in PolyCollection (and seem to recall I used your files as inspiration in the AppVeyor case), will do the same for MultiIndex and Flyweight when I find the time.  
  
Thanks again

---

## Comment 5

> Username: joaquintides  
> Created at: 2018-09-03 07:56:03 UTC  
> Url: https://github.com/boostorg/multi_index/issues/15#issuecomment-418032350  

Additionally, I made `test_key` execution conditional on `BOOST_MULTI_INDEX_KEY_SUPPORTED` being defined:  
  
https://github.com/boostorg/multi_index/blob/d6cd661119013386da240f745364b697e8fd240f/test/Jamfile.v2#L41-L45  
  
which has the effect that the corresponding cell of the test matrix shows blank in pre-C++17 compilers.
