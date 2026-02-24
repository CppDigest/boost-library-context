# #114 - ill-formed code in include/boost/nowide/utf/convert.hpp (incorrect use of template keyword) [Closed]

> Username: cmeerw  
> Created at: 2020-09-25 08:47:12 UTC  
> Updated at: 2020-09-28 13:11:18 UTC  
> Closed at: 2020-09-28 13:11:18 UTC  
> Url: https://github.com/boostorg/nowide/issues/114  

nowide/include/boost/nowide/utf/convert.hpp, line 38:  
```  
code_point c = utf_traits<CharIn>::template decode(source_begin, source_end);  
```  
nowide/include/boost/nowide/utf/convert.hpp, line 49:  
```  
buffer = utf_traits<CharOut>::template encode(c, buffer);  
```  
see http://eel.is/c++draft/temp.names#5 and https://wg21.cmeerw.net/cwg/issue96  
  
> A name prefixed by the keyword template shall be a template-id or the name shall refer to a class template or an alias template.  
  
and the example clarifies:  
```  
template <class T> void f(T t) {  
  A<T> a;  
  a.template f<>(t);                    // OK: calls template  
  a.template f(t);                      // error: not a template-id  
}  
```
