# #1000 - Can the value_from function add iterator parameters, such as value_from (Iterator begin, Iterator end) [Closed]

> Username: wcy168  
> Created at: 2024-04-19 08:45:59 UTC  
> Updated at: 2024-10-09 13:52:42 UTC  
> Closed at: 2024-10-09 13:52:42 UTC  
> Url: https://github.com/boostorg/json/issues/1000  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
You can find the version number in the file `<boost/version.hpp>`  
  
### Steps necessary to reproduce the problem  
  
A small compiling program is the best. If your code is  
public, you can provide a link to the repository.  
  
### All relevant compiler information  
  
If you are unable to compile please include the type and  
version of compiler you are using as well as all compiler  
output including the error message, file, and line numbers  
involved.  
  
The more information you provide the sooner your issue  
can get resolved!

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-04-19 09:06:42 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2066151537  

You didn't explain the desired semantics, so I assume it's supposed to produces a `json::value` that stores an `array`. So, my questions are these.  Why converting a sequence object doesn't work for you? Where do you get the iterators?

---

## Comment 2

> Username: wcy168  
> Created at: 2024-04-21 09:58:56 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2067986547  

> You don't have the required semantics to explain, so I think it should produce a store. Why doesn't converting sequence objects work for you? Where do you get iterators?`json::value``array`  
  
Due to the large amount of data in my sequential container, I can only send a part of it at a time. Therefore, I need to serialize just this portion, so I expect to transmit the iterator range.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-04-21 11:09:10 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2068005372  

But can't you wrap two iterators into a range-like class? This one should work: https://www.boost.org/doc/libs/1_85_0/libs/range/doc/html/range/reference/utilities/iterator_range.html  
But if you have access to C++20, this should also work: https://en.cppreference.com/w/cpp/ranges/subrange

---

## Comment 4

> Username: wcy168  
> Created at: 2024-04-22 02:47:07 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2068387139  

> But can't you wrap two iterators into a range-like class? This one should work: https://www.boost.org/doc/libs/1_85_0/libs/range/doc/html/range/reference/utilities/iterator_range.html But if you have access to C++20, this should also work: https://en.cppreference.com/w/cpp/ranges/subrange  
  
good idea，thank you

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-04-22 07:17:34 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2068658583  

So, did it work for you? Can I close this?

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-10-09 13:52:42 UTC  
> Url: https://github.com/boostorg/json/issues/1000#issuecomment-2402414001  

Silentium videtur confessio.
