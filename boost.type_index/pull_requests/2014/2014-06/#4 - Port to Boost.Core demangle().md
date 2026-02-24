# #4 Port to Boost.Core demangle() [Merged]

> Username: Lastique  
> Created at: 2014-06-11 19:41:28 UTC  
> Updated at: 2014-06-16 07:10:25 UTC  
> Merged at: 2014-06-16 05:31:49 UTC  
> Closed at: 2014-06-16 05:31:49 UTC  
> Url: https://github.com/boostorg/type_index/pull/4  

The port removes some conditional code from pretty_name() implementation. As a side effect, this improves portability (AFAICT, the previous version wouldn't do demangling on clang).  
  
Additionally, the commit fixes a possible buffer overrun if demangle() returns a string equal to cvr_saver_name or cvr_saver_name with trailing spaces.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-06-12 09:58:20 UTC  
> Url: https://github.com/boostorg/type_index/pull/4#issuecomment-45850160  

By providing this pull request you add some regressions:  
- Errors in demangling become silently ignored  
- Additional string construction result in performance degradation.  
  
First one is critical.   
(I need to tune the MSVC demangling too - there is an additional string construction that can be avoided, but that's a separate story).  
  
How about adding a `const char* demangle_ptr(const char*) noexcept` that returns nullptr in case of error and `demangle_ptr_free(const char*)  noexcept` methods to the Core? Such interfaces are light, suit TypeIndex's needs and still leave all the workarounds in one place.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-06-12 18:27:19 UTC  
> Url: https://github.com/boostorg/type_index/pull/4#issuecomment-45929537  

I changed the implementation as you requested.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2014-06-16 06:12:59 UTC  
> Url: https://github.com/boostorg/type_index/pull/4#issuecomment-46144005  

Thanks a lot for the patch!

---
