# #758 - urls::format does not serialize integer zero [Closed]

> Username: edtanous  
> Created at: 2023-06-21 15:45:56 UTC  
> Updated at: 2023-06-29 05:32:54 UTC  
> Closed at: 2023-06-29 05:32:54 UTC  
> Url: https://github.com/boostorg/url/issues/758  

```c++  
std::cout << boost::urls::format("/{}", 0).buffer();  
```  
  
The above example prints "/" to the console when written.  Presumably the correct response is "/0".    
  
```c++  
std::cout << boost::urls::format("/{}", 1).buffer();  
```  
  
Does print "/1", so best guess is that there's something special about zero that makes the algorithm return the wrong answer.  Negative numbers appear to work as well.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-06-21 15:54:34 UTC  
> Url: https://github.com/boostorg/url/issues/758#issuecomment-1601099370  

0 matches a pointer argument type
