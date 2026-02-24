# #18 - One unit test is failing [Closed]

> Username: jefftrull  
> Created at: 2017-10-19 19:51:06 UTC  
> Updated at: 2017-10-26 19:59:39 UTC  
> Closed at: 2017-10-19 22:23:41 UTC  
> Url: https://github.com/boostorg/wave/issues/18  

Both in my own build (Ubuntu 17.04 with gcc) and in Boost's own continuous integration builds (e.g. [this one](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc5-g14-Docker-64on64-boost-bin-v2-libs-wave-test-build-testwave-test-clang-gnu-linux-5-0~gnu14-debug-link-static-threadapi-pthread-threading-multi.html)), the test added in 2013 for MSVC's proprietary long integer literals `t_9_024` is failing.  It looks like some extra configuration is necessary to ensure MSVC support in Wave is properly enabled for testing.

---

## Comment 1

> Username: jefftrull  
> Created at: 2017-10-26 19:55:17 UTC  
> Url: https://github.com/boostorg/wave/issues/18#issuecomment-339782254  

btw I believe this is the same as [TRAC 10730](https://svn.boost.org/trac10/ticket/10730) and thus it can be closed.

---

## Comment 2

> Username: hkaiser  
> Created at: 2017-10-26 19:59:39 UTC  
> Url: https://github.com/boostorg/wave/issues/18#issuecomment-339783463  

Yes, thanks, I closed that ticket.
