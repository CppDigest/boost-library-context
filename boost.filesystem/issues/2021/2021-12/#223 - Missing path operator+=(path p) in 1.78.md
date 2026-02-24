# #223 - Missing path operator+=(path p) in 1.78 [Closed]

> Username: achow101  
> Created at: 2021-12-20 05:39:20 UTC  
> Updated at: 2021-12-23 11:12:12 UTC  
> Closed at: 2021-12-23 11:11:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/223  

I recently updated to boost 1.78 and a project which uses boost::filesystem no longer compiles, emitting the error:  
  
```  
error: no match for ‘operator+=’ (operand types are ‘fs::path’ and ‘std::remove_reference<fs::path&>::type’ {aka ‘fs::path’})  
```  
  
It seems to be that `path& operator+=(const path& p);` is missing from `filesystem/path.hpp` in 1.78 (as it exists in previous versions), although the documentation states that this function still exists.  
  
If I change to using `concat(p)`, it works, but `path& concat(const path& p);` is not available in older versions of boost.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-12-20 07:16:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/223#issuecomment-997659035  

The `path` argument is accepted by [this](https://github.com/boostorg/filesystem/blob/04c6e582be42dbce428d5f04ca14a8d23caafedd/include/boost/filesystem/path.hpp#L433-L440) `operator+=`. Please provide a small compilable repro.

---

## Comment 2

> Username: achow101  
> Created at: 2021-12-20 16:16:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/223#issuecomment-998071304  

It seems like the issue occurs when something subclasses `boost::filesystem::path`. https://github.com/achow101/super-carnival exhibits this failure.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-12-23 11:12:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/223#issuecomment-1000230207  

Thanks for the report.  
  
BTW, you can work around this by explicitly casting the argument to `boost::filesystem::path`.
