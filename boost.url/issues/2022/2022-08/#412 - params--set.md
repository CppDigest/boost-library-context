# #412 - params::set [Closed]

> Username: vinniefalco  
> Created at: 2022-08-16 18:23:33 UTC  
> Updated at: 2022-09-11 23:52:34 UTC  
> Closed at: 2022-09-11 23:52:34 UTC  
> Url: https://github.com/boostorg/url/issues/412  

We need  
```  
std::size_t params::set( string_view key, string_view value );  
```  
  
Or even a variadic version  
```  
template<class... StringViews>  
std::size_t params::set( string_view key, string_view value, StringViews... values );  
```  
  
However we need to decide what to do with existing matching keys. Should we delete them all first? Only replace the first one? Replace each existing in the variadic case, and switch to appending when needed?  
  
We might also consider having `set_all`  and `set_one` (or just `set`) which appropriate corresponding behaviors.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-16 18:32:05 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1217002049  

Peter: erase all previous matches, don't bother with variadic

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-16 18:49:23 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1217018523  

`std::initializer_list< string_view >` instead of variadic could be a possibility

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-16 18:50:52 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1217019962  

Something like `?q=[1,2]` is equivalent to `?q=1&q=2`  
  
In URI.js, this would be something like  
  
```  
set("q", {"1", "2"})  
```  
  
We could add an overload if there is sufficient... _clamour_

---

## Comment 4

> Username: anarthal  
> Created at: 2022-08-16 21:12:26 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1217170874  

I'm with Peter.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-08-16 23:57:46 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1217288143  

Quoting the review (for reference)  
  
> Something I've found when playing with mutating URLs is that modifying  
a query parameter to a certain value is a little bit verbose:  
  
```cpp  
url u; // get it from somewhere  
auto it = u.params().find("k");  
if (it == u.params().end())  
{  
    u.params().append("k", "value");  
}  
else  
{  
    u.params().replace_value(it, "value");  
}  
```  
  
> I had this use case in the last web application I wrote (although it  
wasn't C++).  
I don't know if this is a common use case, but I'd consider adding a function  
that simplifies it (i.e. params::set_value(string_view key, string_view value).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-09-11 23:52:34 UTC  
> Url: https://github.com/boostorg/url/issues/412#issuecomment-1243071715  

This is done
