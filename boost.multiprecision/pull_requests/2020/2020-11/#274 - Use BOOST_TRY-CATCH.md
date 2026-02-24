# #274 Use BOOST_TRY/CATCH [Merged]

> Username: galeone  
> Created at: 2020-11-20 08:51:01 UTC  
> Updated at: 2020-12-30 18:54:02 UTC  
> Merged at: 2020-12-30 18:54:02 UTC  
> Closed at: 2020-12-30 18:54:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/274  

Using the BOOST_TRY/BOOST_CATCH/BOOST_END_CATCH macros instead of `try`/`catch` allows using the multiprecision library with boost configured with `BOOST_NO_EXCEPTIONS=1` and `BOOST_EXCEPTION_DISABLE=1`

---
