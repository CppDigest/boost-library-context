# #255 - Typos in Boost 1.87 documentation [Closed]

> Username: leha-bot  
> Created at: 2025-03-14 09:15:52 UTC  
> Updated at: 2025-04-14 21:14:27 UTC  
> Closed at: 2025-04-14 21:14:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/255  

I found some typos in https://www.boost.org/doc/libs/1_87_0/doc/html/interprocess/quick_guide.html  
, e.g.:  
```cpp  
int main (int argc, char *[2] "MyName"[])  
```  
`"MyName"` is used instead of `argv` in main() signature (and in another places).  
  
The Boost 1.86 documentation doesn't contain this typo:  
https://www.boost.org/doc/libs/1_86_0/doc/html/interprocess/quick_guide.html  
```cpp  
int main (int argc, char *argv[])  
```  
  
Looks like typo or documentation tool bug
