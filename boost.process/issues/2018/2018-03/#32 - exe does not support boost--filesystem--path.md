# #32 - exe does not support boost::filesystem::path [Closed]

> Username: sarum9in  
> Created at: 2018-03-02 16:39:47 UTC  
> Updated at: 2018-06-18 01:51:01 UTC  
> Closed at: 2018-06-18 01:51:01 UTC  
> Url: https://github.com/boostorg/process/issues/32  

Documentation says  
```text  
The following expressions are valid, with `value` being either a C-String or  
a `std::basic_string` with `char` or `wchar_t` or a `boost::filesystem::path`.  
```  
However there is no `operator()(const boost::filesystem::path &)` overload. `exe = boost::filesystem::path()` fails with `no viable overloaded '='` error. By looking on similar code in `start_dir` I think there should be explicit overload for `boost::filesystem::path`.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-03-02 22:50:37 UTC  
> Url: https://github.com/boostorg/process/issues/32#issuecomment-370076091  

If you pass in any instance of `boost::filesystem::path` it will be automatically considered a path to an exe. But fair point, the overload is lacking, I'll add that.

---

## Comment 2

> Username: sarum9in  
> Created at: 2018-03-03 00:17:43 UTC  
> Url: https://github.com/boostorg/process/issues/32#issuecomment-370095151  

My point was that code below does not work:  
```cpp  
boost::filesystem::path exe = ...;  
boost::filesystem::path start_dir = ...;  
std::vector<std::string> args = ...;  
boost::process::child(  
    boost::process::exe = exe,  // this fails  
    boost::process::args = args,  
    boost::process::start_dir = start_dir,  // this is fine  
    ...);  
```  
But this one does:  
```cpp  
boost::process::child(  
    boost::process::exe = exe.native(),  
    boost::process::args = args,  
    boost::process::start_dir = start_dir,  
    ...);  
```
