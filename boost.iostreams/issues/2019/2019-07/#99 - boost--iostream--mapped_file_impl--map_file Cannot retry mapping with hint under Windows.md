# #99 - boost::iostream::mapped_file_impl::map_file  Cannot retry mapping with hint under Windows [Open]

> Username: ksharenkov  
> Created at: 2019-07-16 03:39:10 UTC  
> Updated at: 2019-07-16 07:04:15 UTC  
> Url: https://github.com/boostorg/iostreams/issues/99  

Code in mapped_file.cpp  
```  
void mapped_file_impl::map_file(param_type& p)  
{  
    try {  
        try_map_file(p);  
    } catch (const std::exception&) {  
        if (p.hint) {  
            p.hint = 0;  
            try_map_file(p);  
        } else {  
            throw;  
        }  
    }  
}  
```  
  
As I see, function should try secondary mapping without hint if mapping with hint is failed.  
Windows version has two handles ( `handle_` for file and `mapped_handle_` for mapping). Function `cleanup_and_throw` will be called If mapping failed.  It will close both handles.  But It is necessary to close only mapping handle for second try of mapping.  
So Second try always fails under Windows if nonzero hint is passed.  
*nix version has only one handle and it works.
