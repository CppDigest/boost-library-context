# #184 - Docs: Document minimum Boost requirements [Closed]

> Username: akrzemi1  
> Created at: 2022-06-05 05:41:53 UTC  
> Updated at: 2022-06-10 22:49:20 UTC  
> Closed at: 2022-06-10 22:49:20 UTC  
> Url: https://github.com/boostorg/url/issues/184  

This library requires Boost 1.78 or higher (e.g., for `result<T>` in Boost.System). Document it in the library's requirements.  
  
Of course, such mention will not make sense once the library is accepted to Boost. But so is the case for the current docs, which say the library "requires Boost."

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-08 22:08:40 UTC  
> Url: https://github.com/boostorg/url/issues/184#issuecomment-1150464355  

@alandefreitas we need to make sure this is clear if we do any promotion of the library on social media. The repository sholuld be updated to indicate the requirements and pre-Boost-acceptance installation instructions _before_ any campaigns are launched.
