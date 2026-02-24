# #194 - url is semiregular [Closed]

> Username: akrzemi1  
> Created at: 2022-06-08 20:22:22 UTC  
> Updated at: 2022-06-09 20:07:22 UTC  
> Closed at: 2022-06-09 20:07:22 UTC  
> Url: https://github.com/boostorg/url/issues/194  

https://github.com/CPPAlliance/url/blob/develop/doc/qbk/2.0.quicklook.qbk#L396 says   
> Objects of type [url](https://192.url.prtest.cppalliance.org/libs/url/doc/html/url/ref/boost__urls__url.html) are first-class. They can be copied, moved, and assigned as needed.  
  
The formal name for it is [std::semiregular](https://en.cppreference.com/w/cpp/concepts/semiregular).

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-06-08 20:30:32 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150380554  

[std::semiregular](https://en.cppreference.com/w/cpp/concepts/semiregular) is the concept for   
  
> copied, moved, and assigned  
  
right?  
  
But is this expanding on what being "first-class" mean or did @vinniefalco mean something else as "first-class"?

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-06-08 20:31:43 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150381618  

Why does [std::semiregular](https://en.cppreference.com/w/cpp/concepts/semiregular) imply it can be moved?

---

## Comment 3

> Username: akrzemi1  
> Created at: 2022-06-08 20:36:19 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150387310  

`std::copyable` implies `std::movable`.  
In order to copy in all cases, you need to be able to write:  
  
```c++  
T x = std::declval<T>();  
```  
  
And this is a move.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2022-06-08 20:37:10 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150388269  

Oh. I just saw that.  
  
Now I'm not sure a `url` is [std::default_initializable](http://en.cppreference.com/w/cpp/concepts/default_initializable)<T>;

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-06-08 20:39:10 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150390103  

Yes. It is.   
  
```cpp  
url() noexcept;  
```  
  
wasn't defined before.

---

## Comment 6

> Username: alandefreitas  
> Created at: 2022-06-08 20:41:42 UTC  
> Url: https://github.com/boostorg/url/issues/194#issuecomment-1150392782  

Actually, it's also https://en.cppreference.com/w/cpp/concepts/regular
