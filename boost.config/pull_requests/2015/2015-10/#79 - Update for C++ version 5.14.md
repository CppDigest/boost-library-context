# #79 Update for C++ version 5.14 [Merged]

> Username: akumta  
> Created at: 2015-10-01 18:26:23 UTC  
> Updated at: 2015-10-04 16:22:42 UTC  
> Merged at: 2015-10-04 16:22:42 UTC  
> Closed at: 2015-10-04 16:22:42 UTC  
> Url: https://github.com/boostorg/config/pull/79  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-01 18:56:17 UTC  
> Url: https://github.com/boostorg/config/pull/79#issuecomment-144816182  

Is 5.14 actually released yet?  Just wondering.

---

## Comment 2

> Username: akumta  
> Created_at: 2015-10-01 19:57:55 UTC  
> Url: https://github.com/boostorg/config/pull/79#issuecomment-144830909  

No.  
  
On 10/01/15 11:56, jzmaddock wrote:  
  
> Is 5.14 actually released yet? Just wondering.  
>   
> —  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/config/pull/79#issuecomment-144816182.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2015-10-02 08:55:01 UTC  
> Url: https://github.com/boostorg/config/pull/79#issuecomment-144962803  

On 01/10/2015 20:57, akumta wrote:  
  
> No.  
>   
> OK thanks.  I was asking because there are type_traits failures against   
> 5.14 not present in 5.13 (see   
> http://www.boost.org/development/tests/develop/developer/output/oracle-sparc-S2-next-cpp11-boost-bin-v2-libs-type_traits-test-common_type_2_test-test-sun-next_cpp11-release-threading-multi.html)   
> and these ripple through to other libraries, see message:   
> http://lists.boost.org/Archives/boost/2015/10/225794.php  
  
I assume these are new compiler bugs/regressions?  
  
In any case I will provisionally merge your changes, pending 5.14's   
official release.

---
