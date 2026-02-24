# #311 - weakly_canonical doesn't seem to work on Windows on pathes starting with "../" [Closed]

> Username: h01G3r  
> Created at: 2024-05-23 16:38:44 UTC  
> Updated at: 2024-07-03 20:08:16 UTC  
> Closed at: 2024-07-03 20:08:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/311  

e.g. for the Code  
`auto tmp = boost::filesystem::weakly_canonical("../foo", "C:/LocalData");`  
I would expect a result of "C:\\foo", but I get "C:\\LocalData\\foo" instead,  
  
This was tested on 1.85.0 release code-base, Windows 10, Visual Studio 2014.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-06-29 16:10:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/311#issuecomment-2198247444  

Are you using Boost.Filesystem v3 or v4?

---

## Comment 2

> Username: Lastique  
> Created at: 2024-07-03 20:08:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/311#issuecomment-2207148313  

Should be fixed now. Thanks for the report.
