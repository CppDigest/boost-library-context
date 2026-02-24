# #44 - Check for UTF-8 before changing args? [Closed]

> Username: chambm  
> Created at: 2019-04-08 14:22:54 UTC  
> Updated at: 2019-04-09 14:08:56 UTC  
> Closed at: 2019-04-09 14:08:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/44  

I have a case I don't quite understand where arguments are being passed to my program (msvc14.1 toolset) already encoded as UTF-8. Thus when I run `boost::nowide::args` it just mangles it. Would it make sense to check for UTF-8 encoding?

---

## Comment 1

> Username: chambm  
> Created at: 2019-04-08 17:50:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/44#issuecomment-480935694  

I think the problem is I'm passing the UTF-8 encoded strings to a std::system call.  The resulting process has its `GetCommandLineW()` arguments encoded in UTF-8.  I can think of 2 ways to fix it:  
1. Have a boost::nowide version of `std::system()` that uses `_wsystem` on Windows.   
2. Have boost::nowide::args detect that the return value from `GetCommandLineW()` is equal to the provided `argv` and thus do nothing.

---

## Comment 2

> Username: artyom-beilis  
> Created at: 2019-04-09 11:39:54 UTC  
> Url: https://github.com/boostorg/nowide/issues/44#issuecomment-481215873  

`int main(int argc,char **argv)` under Windows get ANSI encoding and not UTF-8.  
  
When you use `boost::nowide::args` it replaces them with UTF-8 encoded ones it gets from `GetCommandLineW` which are encoded as UTF-16  
  
> Have a boost::nowide version of std::system() that uses _wsystem on Windows.  
  
`boost::nowide::system` uses _wsystem under the hood  
  
> Have boost::nowide::args detect that the return value from GetCommandLineW() is equal to the provided argv and thus do nothing.  
  
Why to check it? They will only match in case of ASCII parameters in any other case they will be different. It would only create unexpected behavior

---

## Comment 3

> Username: chambm  
> Created at: 2019-04-09 14:08:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/44#issuecomment-481267745  

Ugh, I hadn't seen the existing `boost::nowide::system()`. Sorry!
