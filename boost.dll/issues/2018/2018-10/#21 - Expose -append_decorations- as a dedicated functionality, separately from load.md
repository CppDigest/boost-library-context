# #21 - Expose "append_decorations" as a dedicated functionality, separately from load [Closed]

> Username: Firefly35  
> Created at: 2018-10-11 08:53:09 UTC  
> Updated at: 2018-10-26 07:38:23 UTC  
> Closed at: 2018-10-26 07:38:12 UTC  
> Url: https://github.com/boostorg/dll/issues/21  

In some cases, I'd expect to be able to compute the complete full path of the shared library as an boost::filesystem::path prior to load the library.  
I'd like to have a method (maybe static ?) such as   
`boost::filesystem::path shared_library::append_decorations(const boost::filesystem::path & path)` exposed in shared_library class.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-10-26 07:38:11 UTC  
> Url: https://github.com/boostorg/dll/issues/21#issuecomment-433316693  

I'm afraid this is not possible. `append_decorations` tries to guess the name, so it may try to open multiple different names, stopping at first success.  
  
If you have some consistent naming of your libraries/plugins then you can use the suffix function https://github.com/boostorg/dll/blob/8c641504e9a7ee6ccb16dfa77ed1fc089f4a9675/include/boost/dll/shared_library.hpp#L510 to get the right extension and form a library name.
