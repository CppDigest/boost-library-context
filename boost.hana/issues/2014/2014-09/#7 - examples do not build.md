# #7 - examples do not build [Closed]

> Username: mgaunard  
> Created at: 2014-09-16 14:13:18 UTC  
> Updated at: 2014-09-16 15:29:08 UTC  
> Closed at: 2014-09-16 15:29:08 UTC  
> Url: https://github.com/boostorg/hana/issues/7  

```  
../example/foldable.cpp:30:48: error: no member named 'str' in 'std::basic_ostream<char>'  
            return (std::ostringstream{} << x).str();  
```  
  
This is with clang 3.6.0 885f51439b619bfc42e47c70bd3404ceedcf259f on Linux.

---

## Comment 1

> Username: mgaunard  
> Created at: 2014-09-16 14:27:37 UTC  
> Url: https://github.com/boostorg/hana/issues/7#issuecomment-55750765  

`static_cast<std::ostringstream&>(std::ostringstream{} << x).str();` works well

---

## Comment 2

> Username: ldionne  
> Created at: 2014-09-16 15:27:01 UTC  
> Url: https://github.com/boostorg/hana/issues/7#issuecomment-55760621  

Here's what I think happens: `std::ostringstream{} << x` returns a `std::basic_ostream<char>` when it is used with a `std::string`, because the `operator<<` is defined as:  
  
``` cpp  
template<class _CharT, class _Traits, class _Allocator>  
basic_ostream<_CharT, _Traits>&  
operator<<(basic_ostream<_CharT, _Traits>& __os,  
           const basic_string<_CharT, _Traits, _Allocator>& __str);  
```  
  
Then, we try to `.str()` that `basic_ostream`, which results in the provided error. I had never thought about the fact that `operator<<` would return a `basic_ostream`; I find that badly designed and counterintuitive. Still, I'll fix the examples.  
  
Thanks for reporting.
