# #119 - mapped_file documentation is incomplete [Open]

> Username: jm4R  
> Created at: 2020-07-03 08:37:50 UTC  
> Updated at: 2020-07-03 08:37:50 UTC  
> Url: https://github.com/boostorg/iostreams/issues/119  

`mapped_file` documentation doesn't say:  
1) if destructor closes file  
2) how we should deal with IO errors (`std::copy(file.data(), ...)` can't throw, I guess?)  
  
One should read source code to get that information:  
https://stackoverflow.com/questions/26608264/what-exceptions-does-boost-mapped-file-source-throw  
https://stackoverflow.com/questions/7342045/are-boost-mapped-files-raii
