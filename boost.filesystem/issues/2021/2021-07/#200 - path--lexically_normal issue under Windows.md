# #200 - path::lexically_normal issue under Windows [Closed]

> Username: d0nc1h0t  
> Created at: 2021-07-20 07:29:34 UTC  
> Updated at: 2021-07-20 08:50:56 UTC  
> Closed at: 2021-07-20 08:50:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/200  

After upgrading boost to version 1.77 beta 1, the function path::lexically_normal under Windows OS changed.  
```c++  
  const boost::filesystem::path path{ "C:/Directory/Folder" };  
  const auto normal = path.lexically_normal();  
```  
Boost 1.76 - 'C:\Directory\Folder' (similar to std::filesystem::path)  
Boost 1.77 beta 1 - 'C:/Directory\Folder'

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-20 08:50:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/200#issuecomment-883219426  

Thanks for the report.
