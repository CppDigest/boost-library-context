# #527 - Add check for pmr default_ressource related functions [Open]

> Username: sdebionne  
> Created at: 2025-11-25 08:08:54 UTC  
> Updated at: 2025-11-26 11:43:21 UTC  
> Url: https://github.com/boostorg/config/issues/527  

Some versions of `libc++` on Apple are missing the implementation of the `pmr::get_default_resource()`. Some Boost.GIL tests are impacted and discussed in https://github.com/boostorg/gil/pull/778.  
  
In CMake terms, the check would look something like this:  
  
```  
check_cxx_source_compiles( "  
   #include <${pmr_include}>  
   int main() {  
         ${pmr_ns}::set_default_resource(nullptr);  
         ${pmr_ns}::memory_resource* mr = ${pmr_ns}::get_default_resource();  
         return 0;  
   }  
  "BOOST_HAVE_DEFAULT_RESOURCE)  
```  
  
A polyfill would be easy to add for the missing symbols.

---

## Comment 1

> Username: sdebionne  
> Created at: 2025-11-25 19:55:53 UTC  
> Url: https://github.com/boostorg/config/issues/527#issuecomment-3577346691  

Would it be OK if I add a `boost_no_cxx17_default_resource.ipp` defect test with the above code?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2025-11-26 10:19:10 UTC  
> Url: https://github.com/boostorg/config/issues/527#issuecomment-3580617576  

Sure, do you want to file a PR?

---

## Comment 3

> Username: sdebionne  
> Created at: 2025-11-26 11:43:21 UTC  
> Url: https://github.com/boostorg/config/issues/527#issuecomment-3580930303  

Yes, will do!
