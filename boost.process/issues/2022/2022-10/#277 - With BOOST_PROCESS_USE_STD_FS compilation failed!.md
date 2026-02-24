# #277 - With BOOST_PROCESS_USE_STD_FS compilation failed! [Closed]

> Username: fsmoke  
> Created at: 2022-10-26 09:17:40 UTC  
> Updated at: 2023-05-26 14:09:17 UTC  
> Closed at: 2023-05-26 14:09:00 UTC  
> Url: https://github.com/boostorg/process/issues/277  

Error:  
  
`boost\process\detail\windows\basic_cmd.hpp(163,1): error C2660: 'std::filesystem::path::wstring': function does not take 1 arguments`   
  
inside this piece of code   
  
```  
    static std:: string get_shell(char)    {return shell(). string(codecvt()); }  
    static std::wstring get_shell(wchar_t) {return shell().wstring(codecvt());} // HERE ERROR  
```  
now see declaration of shell_ structure   
  
```  
struct shell_  
{  
    constexpr shell_() {}  
  
    boost::process::filesystem::path operator()() const  
    {  
        return boost::process::detail::api::shell_path();  
    }  
    boost::process::filesystem::path operator()(std::error_code & ec) const noexcept  
    {  
        return boost::process::detail::api::shell_path(ec);  
    }  
};  
```  
  
operator () returns in fact std::filesystem::path, wstring and string methods in this case have no arguments, see https://en.cppreference.com/w/cpp/filesystem/path/string  
  
but in code we can see that codecvt() passed as argument inside string and wsting methods.  
  
So BOOST_PROCESS_USE_STD_FS macro unusable for now.  
  
PS  
Environment:  
Windows 10;  
Microsoft Visual Studio 2022;  
BOOST version is 1.80.0;  
  
Test piece of code(I run git here):  
```  
bp::ipstream is;  
bp::child c(_git_path, bp::start_dir(_project_path),std::vector<std::string>{"rev-parse", "HEAD"}, bp::std_out > is);  
  
c.wait();  
```  
where _git_path and _project_path - std::filesystem::path

---

## Comment 1

> Username: bkaestner  
> Created at: 2022-10-31 14:20:55 UTC  
> Url: https://github.com/boostorg/process/issues/277#issuecomment-1297164970  

I believe this is a duplicate of #263 or #275, possibly fixed by https://github.com/boostorg/process/commit/a7b65bfc44f4af3a9d584fe9347fde0944c0167a.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-10-31 18:22:07 UTC  
> Url: https://github.com/boostorg/process/issues/277#issuecomment-1297490784  

It is.

---

## Comment 3

> Username: fsmoke  
> Created at: 2022-10-31 18:26:02 UTC  
> Url: https://github.com/boostorg/process/issues/277#issuecomment-1297496287  

It seems, yes, it is

---

## Comment 4

> Username: fsmoke  
> Created at: 2023-05-26 14:09:17 UTC  
> Url: https://github.com/boostorg/process/issues/277#issuecomment-1564452372  

fixed at least in boost 1.82
