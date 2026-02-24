# #541 - Cygwin build fails: var_edit_translate_path not declared [Closed]

> Username: Lastique  
> Created at: 2020-02-27 19:19:05 UTC  
> Updated at: 2020-02-28 04:58:22 UTC  
> Closed at: 2020-02-28 04:58:22 UTC  
> Url: https://github.com/boostorg/build/issues/541  

Building `b2` from develop on Cygwin64 fails with the following error:  
  
```  
function.cpp: In function ‘void var_edit_shift(string*, size_t, VAR_EDITS*)’:  
function.cpp:782:5: error: ‘var_edit_translate_path’ was not declared in this scope  
     var_edit_translate_path( out, pos, edits );  
     ^~~~~~~~~~~~~~~~~~~~~~~  
function.cpp:782:5: note: suggested alternative: ‘var_edit_parse’  
     var_edit_translate_path( out, pos, edits );  
     ^~~~~~~~~~~~~~~~~~~~~~~  
     var_edit_parse  
```
