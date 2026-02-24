# #978 - Documentation inconsistency about exceptions [Closed]

> Username: kuznetsss  
> Created at: 2024-02-06 17:38:55 UTC  
> Updated at: 2024-02-06 17:48:56 UTC  
> Closed at: 2024-02-06 17:48:56 UTC  
> Url: https://github.com/boostorg/json/issues/978  

Hi, thanks for the useful library!  
  
I think there are a few inconsistencies about whether function or method throws an exception:  
[value::as_string()](https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__value/as_string/overload1.html):  
<img width="687" alt="Screenshot 2024-02-06 at 17 30 46" src="https://github.com/boostorg/json/assets/15742918/1b73b0fa-9d49-4a4a-a536-4ed88400587e">  
  
[parse()](https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__parse/overload5.html)  
<img width="1164" alt="Screenshot 2024-02-06 at 17 34 16" src="https://github.com/boostorg/json/assets/15742918/c76b634c-606b-4f01-9791-527feacec394">  
  
In both cases it says in the description that exception could be thrown but at the same time it says `Exception Safety: strongly guarantee`.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-02-06 17:44:55 UTC  
> Url: https://github.com/boostorg/json/issues/978#issuecomment-1930457245  

"Strong guarantee" means that if the function throws an exception, then any of its arguments, including the object on which the function is called (if it's a member function) aren't changed. You are thinking of the no-throw guarantee.  
  
See this document for more info: https://www.boost.org/community/exception_safety.html (section 3 discusses exception safety guarantees).

---

## Comment 2

> Username: kuznetsss  
> Created at: 2024-02-06 17:48:56 UTC  
> Url: https://github.com/boostorg/json/issues/978#issuecomment-1930463768  

Thanks for the explanation!
