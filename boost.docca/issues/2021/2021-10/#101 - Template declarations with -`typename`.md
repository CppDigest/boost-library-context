# #101 - Template declarations with "`typename`" [Closed]

> Username: alandefreitas  
> Created at: 2021-10-14 19:08:56 UTC  
> Updated at: 2021-10-15 00:11:37 UTC  
> Closed at: 2021-10-15 00:11:37 UTC  
> Url: https://github.com/boostorg/docca/issues/101  

The declaration  
  
```cpp  
template<typename charT, typename traits>  
class basic_string_ref;  
```  
  
generates something very weird:  
  
![image](https://user-images.githubusercontent.com/5369819/137380392-82c5d9a6-5790-4066-b8cc-ba10e410d2f6.png)  
  
while  
  
```cpp  
template<class charT, class traits>  
class basic_string_ref;  
```  
  
generates everything correctly:  
  
![image](https://user-images.githubusercontent.com/5369819/137380432-4ee45134-9570-4b94-9736-9f85c44bd1f6.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-14 20:08:50 UTC  
> Url: https://github.com/boostorg/docca/issues/101#issuecomment-943690312  

Yeah that "something weird" is an error message. It means something needs to be fixed in the xslt scripts so it is trying to get your attention with red text.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2021-10-14 20:26:37 UTC  
> Url: https://github.com/boostorg/docca/issues/101#issuecomment-943704168  

Maybe   
  
```cpp  
template<typename charT, typename traits>  
class basic_string_ref;  
```  
  
requires something that  
  
```cpp  
template<class charT, class traits>  
class basic_string_ref;  
```  
  
doesn't? It seems weird though.  
  
>  It means something needs to be fixed in the xslt scripts so it is trying to get your attention with red text.  
  
@vinniefalco In custom-overrides.xsl or the docca xslt scripts? There's nothing that seems related to that in `custom-overrides.xsl`, which is the only xsl file in boost.utility and boost.json.  
  
Maybe that's not a bug and some sort of discussion would be more appropriate than an issue.

---

## Comment 3

> Username: evanlenz  
> Created at: 2021-10-14 20:29:58 UTC  
> Url: https://github.com/boostorg/docca/issues/101#issuecomment-943706609  

It's a docca bug, but have no fear, I'm on it. :-)
