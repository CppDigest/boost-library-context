# #46 haiku: add platform support [Closed]

> Username: jessicah  
> Created at: 2014-11-16 01:00:22 UTC  
> Updated at: 2016-04-02 17:07:17 UTC  
> Closed at: 2015-02-05 18:13:54 UTC  
> Url: https://github.com/boostorg/config/pull/46  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-11-16 08:40:12 UTC  
> Url: https://github.com/boostorg/config/pull/46#issuecomment-63210405  

OK, but I have a couple of comments: new file should have your copyright not mine, and:  
  
+#define GLIBC_HAVE_LONG_LONG  
  
Looks suspicious to me - won't that change the behavior of std lib headers?  If so it's a really bad idea to define it in a header which may not be the first #include.  What was the logic in defining it?

---

## Comment 2

> Username: jessicah  
> Created_at: 2014-11-16 08:55:33 UTC  
> Url: https://github.com/boostorg/config/pull/46#issuecomment-63210862  

The GLIBC_HAVE_LONG_LONG comes from the original patch for Boost 1.55.0 maintained by the Haiku Project. It also looked suspicious to me. Having run grep, I can't see any uses of this #define by anything either, so will remove.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-02-05 18:13:54 UTC  
> Url: https://github.com/boostorg/config/pull/46#issuecomment-73096590  

Merged to develop

---

## Comment 4

> Username: Flast  
> Created_at: 2016-04-02 17:07:17 UTC  
> Url: https://github.com/boostorg/config/pull/46#discussion_r58296322  

I don't know why those lines are needed, but those break compilation on recent gcc w/ c++11 support, e.g. [Test output: Flast-Haiku-R1-gcc-4.8.5~gnu++11 - bind - bind_and_or_test / gcc-4.8.5](http://www.boost.org/development/tests/develop/developer/output/Flast-Haiku-R1-gcc-4-8-5~gnu++11-boost-bin-v2-libs-bind-test-bind_and_or_test-test-gcc-4-8-5-debug.html).  
Does Haiku team remove those features from older gcc?

---
