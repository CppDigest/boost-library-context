# #165 - Link error with shared library and memory_resource inline members [Closed]

> Username: vinniefalco  
> Created at: 2020-09-26 19:10:28 UTC  
> Updated at: 2020-10-25 14:21:50 UTC  
> Closed at: 2020-10-25 14:21:50 UTC  
> Url: https://github.com/boostorg/container/issues/165  

When building Boost.JSON with `link=shared` (b2), this link error appears:  
```  
msvc.link C:\Users\vinnie\src\boost\bin.v2\libs\json\bench\msvc-14.2\debug\threading-multi\bench.exe  
bench.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) public: void __thiscall boost::container::pmr::memory_resource::deallocate(void *,unsigned int,unsigned int)" (__imp_?deallocate@memory_resource@pmr@container@boost@@QAEXPAXII@Z) referenced in function "public: void __thiscall boost::json::detail::string_impl::destroy(class boost::json::storage_ptr const &)" (?destroy@string_impl@detail@json@boost@@QAEXABVstorage_ptr@34@@Z)  
C:\Users\vinnie\src\boost\bin.v2\libs\json\bench\msvc-14.2\debug\threading-multi\bench.exe : fatal error LNK1120: 1 unresolved externals  
```  
  
We believe it is because the inline member function definitions are not being generated and exported in the resulting shared library. A possible solution is to decorate the declaration with the export macro.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-10-24 19:33:06 UTC  
> Url: https://github.com/boostorg/container/issues/165#issuecomment-716044124  

This happens when `memory_resource::allocate` or `memory_resource::deallocate` don't get inlined. Since the whole class is exported, out of line definitions aren't emitted, because they are declared to be exported from the DLL.  
  
The fix here is probably to remove `BOOST_CONTAINER_DECL` from `memory_resource`. The class has no out of line member functions, so exporting it shouldn't be necessary.  
  
It might be better to mark the destructor `= default;` on compilers that support that, but it should work without that too.
