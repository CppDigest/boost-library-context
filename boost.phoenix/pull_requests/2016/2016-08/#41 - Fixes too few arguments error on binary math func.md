# #41 Fixes too few arguments error on binary math func. [Merged]

> Username: Flast  
> Created at: 2016-08-19 04:38:51 UTC  
> Updated at: 2016-08-19 04:42:41 UTC  
> Merged at: 2016-08-19 04:42:39 UTC  
> Closed at: 2016-08-19 04:42:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/41  

Because incorrect decltype usage within result.

---

## Comment 1

> Username: Flast  
> Created_at: 2016-08-19 04:41:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/41#discussion_r75426818  

I suspect that this condition aims to detect trailing result type availability, i.e. `BOOST_NO_CXX11_TRAILING_RESULT_TYPES`.  
However, I don't remove that at this time because the PR is opened for fixing a bug.

---

## Comment 2

> Username: Flast  
> Created_at: 2016-08-19 04:42:33 UTC  
> Url: https://github.com/boostorg/phoenix/pull/41#issuecomment-240926533  

c.f. http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dg4-7-Docker-64on64-boost-bin-v2-libs-phoenix-test-cmath-test-gcc-4-7~c++11-debug.html

---
