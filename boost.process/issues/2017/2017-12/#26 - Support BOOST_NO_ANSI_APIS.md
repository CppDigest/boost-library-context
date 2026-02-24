# #26 - Support BOOST_NO_ANSI_APIS [Closed]

> Username: hia3  
> Created at: 2017-12-18 03:48:36 UTC  
> Updated at: 2019-01-04 23:44:20 UTC  
> Closed at: 2018-02-06 23:19:46 UTC  
> Url: https://github.com/boostorg/process/issues/26  

#define BOOST_NO_ANSI_APIS 1  
    #include <boost/process.hpp>  
    int main() {}  
  
Fails to compile with:   
  
    boost/process/detail/windows/locale.hpp(44): error C3861: 'AreFileApisANSI': identifier not found

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-12-18 18:51:51 UTC  
> Url: https://github.com/boostorg/process/issues/26#issuecomment-352522831  

Thanks for the report. Can you share what compiler and what windows version you use?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2017-12-19 19:17:55 UTC  
> Url: https://github.com/boostorg/process/issues/26#issuecomment-352859143  

Out of curiosity, why do you use `BOOST_NO_ANSI_APIS` ?

---

## Comment 3

> Username: hia3  
> Created at: 2017-12-19 19:59:44 UTC  
> Updated at: 2017-12-19 20:57:45 UTC  
> Url: https://github.com/boostorg/process/issues/26#issuecomment-352869829  

> Out of curiosity, why do you use BOOST_NO_ANSI_APIS ?  
  
ANSI is an endless source of bugs, more on this here: http://utf8everywhere.org/  
  
You might know already, that Boost.Nowide is accepted into Boost (not included yet), here is what it recommends: http://cppcms.com/files/nowide/html/index.html#using_custom  
  
Also I think I had some compilation problem trying to compile some version of Boost with some vesion of clang on Windows, but now I can't tell what the problem was.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2017-12-19 20:31:15 UTC  
> Url: https://github.com/boostorg/process/issues/26#issuecomment-352877754  

Ok, well then let's turn that into a supporting the `BOOST_NO_ANSI_APIS` issue then, so we're clear on the goal here.

---

## Comment 5

> Username: hia3  
> Created at: 2019-01-04 23:44:20 UTC  
> Url: https://github.com/boostorg/process/issues/26#issuecomment-451602281  

Thanks for the fix.  
Although, the example compiles now, it is still ill-formed.  
  
Compiling with `-fno-delayed-template-parsing` (clang) produces:  
  
[boost/process/detail/windows/basic_pipe.hpp:147:13](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/basic_pipe.hpp#L147): error: cannot initialize a parameter of type 'boost::winapi::LPCWSTR_' (aka 'const wchar_t *') with an rvalue of type 'const char *'  
            name.c_str(),  
            ^~~~~~~~~~~~  
boost/winapi/pipes.hpp:265:14: note: passing argument to parameter 'lpName' here  
    LPCWSTR_ lpName,  
             ^  
  
It compiles without the flag because [no diagnostic required](https://github.com/cplusplus/draft/blob/a88d02d8d77e730bbf2730b6988a8b63319496c3/source/templates.tex#L4185).
