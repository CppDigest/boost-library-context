# #78 Omit redundant arguments in variadic template documentation [Closed]

> Username: alandefreitas  
> Created at: 2021-11-01 21:29:36 UTC  
> Updated at: 2021-11-02 19:55:25 UTC  
> Closed at: 2021-11-02 19:55:25 UTC  
> Url: https://github.com/boostorg/url/pull/78  

## Before  
  
<img width="389" alt="image" src="https://user-images.githubusercontent.com/5369819/139744542-b244bde4-d61c-4d01-a95b-76329122fd41.png">  
  
  
<img width="1034" alt="image" src="https://user-images.githubusercontent.com/5369819/139744412-3b45d429-31c4-4d43-b6e7-da1d041e1778.png">  
  
## After  
  
<img width="362" alt="image" src="https://user-images.githubusercontent.com/5369819/139744505-ddc00100-d120-44bb-9643-cf8c94f5f65a.png">  
  
  
<img width="1129" alt="image" src="https://user-images.githubusercontent.com/5369819/139744442-4f121075-d438-4693-a97e-b42439b4a419.png">

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2021-11-01 21:38:57 UTC  
> Url: https://github.com/boostorg/url/pull/78#issuecomment-956656999  

@vinniefalco I did exactly what you suggested and it looks much better, but I've been wondering if we shouldn't use the same argument names for both versions.   
  
That could be safer and easier to maintain because we would really just omit the arguments we don't want in the documentation instead of maintaining two declarations.   
  
For instance,   
  
Before:  
  
```cpp  
#ifdef BOOST_URL_DOCS  
  
template<class... Args>  
bool parse( char const*& it, char const* end, Args&&... args);  
  
#else  
  
template<  
    class T0,  
    class T1,  
    class... Tn>  
bool  
parse(  
    char const*& it,  
    char const* end,  
    error_code& ec,  
    T0&& t0,  
    T1&& t1,  
    Tn&&... tn);  
  
#endif  
```  
  
After:  
  
```cpp  
template<  
#ifndef BOOST_URL_DOCS  
    class Arg0, class Arg1,  
#endif  
    class... Args>  
bool  
parse(  
    char const*& it,  
    char const* end,  
    error_code& ec,  
#ifndef BOOST_URL_DOCS  
    Arg0&& arg0, Arg1&& arg1,  
#endif  
    Args&&... args);  
```  
  
It requires two preprocessor conditionals but it uses a single function declaration.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-11-02 14:29:38 UTC  
> Url: https://github.com/boostorg/url/pull/78#issuecomment-957700222  

An automated preview of the documentation is available at [https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-11-02 19:04:41 UTC  
> Url: https://github.com/boostorg/url/pull/78#issuecomment-958080943  

An automated preview of the documentation is available at [https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-11-02 19:54:39 UTC  
> Url: https://github.com/boostorg/url/pull/78#issuecomment-958119283  

An automated preview of the documentation is available at [https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://78.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
