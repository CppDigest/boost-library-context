# #157 Remove shadow-field warnings. [Merged]

> Username: chrisse74  
> Created at: 2023-01-17 11:51:45 UTC  
> Updated at: 2023-01-21 17:19:12 UTC  
> Merged at: 2023-01-21 17:19:12 UTC  
> Closed at: 2023-01-21 17:19:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/157  

Removes shadow-field warnings by adding an underscore as suffix as already done for other parameters of the affected constructor.  
  
warning: parameter 'level' shadows member inherited from type 'zlib_params' [-Wshadow-field]  
    gzip_params( int level              = gzip::default_compression,

---
