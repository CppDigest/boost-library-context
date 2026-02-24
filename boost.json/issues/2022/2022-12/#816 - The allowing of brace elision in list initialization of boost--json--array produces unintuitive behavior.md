# #816 - The allowing of brace elision in list initialization of boost::json::array produces unintuitive behavior. [Closed]

> Username: ghost  
> Created at: 2022-12-01 01:31:05 UTC  
> Updated at: 2022-12-03 15:14:32 UTC  
> Closed at: 2022-12-02 16:30:02 UTC  
> Url: https://github.com/boostorg/json/issues/816  

### Problem  
The allowing of brace elision in list initialization of boost::json::array produces unintuitive behavior.  
  
Initializing a `std::vector` with another `std::vector` can be done as follows:  
```C++  
std::vector<int> A{{1, 2, 3}};  
std::vector<int> B{A};  
```  
As one would expect, `B` is initialized to contain `[1,2,3]`.  
  
Unfortunately, the same does not hold true for `boost::json::array`, despite the fact that [the documentation](https://www.boost.org/doc/libs/1_80_0/libs/json/doc/html/json/dom.html) says "The interface and performance characteristics [of `boost::json::array`] are similar to [`std::vector`](https://en.cppreference.com/w/cpp/container/vector)."  
```C++  
boost::json::array C{{1, 2, 3}};  
boost::json::array D{C};  
```  
This attempt to use an interface that was allegedly modeled after the `std::vector` interface produces this unimaginable horror for the initial value of `D`: `[[[1,2,3]]]`.  
  
This is an unnecessary inconsistency with `std::vector`'s interface.  
  
### Version of Boost  
  
`BOOST_VERSION 108000`  
  
### Minimal working example  
  
```C++  
#include<boost/json/src.hpp>  
#include<iostream>  
#include<vector>  
  
int main() {  
  std::vector<int> A{{1, 2, 3}};  
  std::vector<int> B{A};  
  std::cout << "[" << A[0];  
  for (size_t i{1}; i < A.size(); i++) {  
    std::cout << "," << A[i];  
  }  
  std::cout << "]" << std::endl;  
  
  boost::json::array C{{1, 2, 3}};  
  boost::json::array D{C};  
  std::cout << D << std::endl;  
  
  return 0;  
}  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-12-01 07:40:57 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1333335079  

The comparison to `std::vector<int>` in this case is not ideal, because `std::vector<int>` cannot contain another `std::vector<int>`, while `json::array` can contain another `json::array`. Making your example work exactly like you it works with vector would make certain array configurations unexpressable.  
  
E.g.  
```c++  
array{}; // JSON [ ]  
array{1}; // JSON [ 1 ]  
array{1, 2}; // JSON [ 1, 2 ]  
array{{}}; // JSON [ [] ]  
array{{1}}; // JSON [ [1] ]  
array{{1, 2, 3}}; // JSON [ [1, 2, 3] ]  
array A{{1, 2, 3}; array{A}; // JSON [ [ [1, 2, 3] ] ], or in other words [ A ]  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2022-12-01 11:48:47 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1333645629  

Using `X x2{ x1 };` for copy construction is bad practice for exactly these reasons; it's ambiguous in meaning. `X x2( x1 );` should be preferred.

---

## Comment 3

> Username: ghost  
> Created at: 2022-12-02 15:12:53 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335392423  

> Making your example work exactly like you it works with vector would make certain array configurations unexpressable.  
  
I don't understand. Why couldn't one just do `array{{}}` to make `JSON []` and `array{{{{}}}}` to make `JSON [[[1, 2, 3]]]` If this was how things worked, it would also be entirely consistent with copy construction, which brings my to my next point:  
  
> Using X x2{ x1 }; for copy construction is bad practice  
  
Source? If the list initializer made sense, your point suddenly vaporizes.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-12-02 15:23:08 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335412528  

`{ x }` is a list of one element. Reusing it for copy construction in the special case where the type of `x` is the same as the LHS creates an ambiguity between these two meanings, and is to be avoided for this reason. A similar problem exists with `std::tuple x2{ x1 };`, which is a tuple of one element containing `x1` when `x1` is not a tuple, and a copy of `x1` when `x1` is a tuple.  
  
Most C++ coding guidelines are gradually converging on "never use {} initialization to call a constructor, only use it for initializing from a list of elements", although this rule is not yet universal. But in the copy constructor case, it's pretty strong.  
  
You're welcome to keep using {} for copy construction, of course, but going around insisting that people need to change their libraries to fit your broken style is counterproductive.

---

## Comment 5

> Username: ghost  
> Created at: 2022-12-02 16:30:02 UTC  
> Updated at: 2022-12-02 16:31:51 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335506045  

> `{ x }` is a list of one element. Reusing it for copy construction in the special case where the type of `x` is the same as the LHS creates an ambiguity between these two meanings, and is to be avoided for this reason. A similar problem exists with `std::tuple x2{ x1 };`, which is a tuple of one element containing `x1` when `x1` is not a tuple, and a copy of `x1` when `x1` is a tuple.  
>   
> Most C++ coding guidelines are gradually converging on "never use {} initialization to call a constructor, only use it for initializing from a list of elements", although this rule is not yet universal. But in the copy constructor case, it's pretty strong.  
>   
> You're welcome to keep using {} for copy construction, of course, but going around insisting that people need to change their libraries to fit your broken style is counterproductive.  
  
Fair enough. All good points.  
  
Most vexing parse refuses to be slain I suppose.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-12-02 17:16:33 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335556940  

> Most vexing parse refuses to be slain I suppose.  
  
The design of language initializer-lists is a pain point. We did the best we could within the constraints of the language, but it isn't perfect. However do note that the library does a pretty great job with it - the conversion of initializer list elements to `json::value` happens on the assignment of the entire list rather than the construction of each individual element! That means that 1. the construction of the initializer list does not allocate memory, and 2. the construction of a `json::value` from each list element uses the memory resource of the destination value. So there's that.

---

## Comment 7

> Username: ghost  
> Created at: 2022-12-02 20:55:08 UTC  
> Updated at: 2022-12-02 20:56:32 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335837342  

Ah! Very cool.  
  
I was taught to ["favor initialization using braces whenever possible,"](https://www.learncpp.com/cpp-tutorial/variable-assignment-and-initialization/) so that's why I wanted to put forth the argument. I still don't exactly understand why the ambiguity wouldn't be removed by simply requiring an extra set of braces, but I suppose the more universal solution is for me to consider which way is the best way to initialize a variable on a case-by-case basis instead of chucking everything in a set of braces by muscle memory. After all, it would make sense to only use so-called _list initialization_ for... _lists_.  
  
By the way, thanks a bunch for putting together such a helpful library. It's been an important part of an orbit propagator I'm writing :)

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-12-03 00:04:51 UTC  
> Updated at: 2022-12-03 00:05:26 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1335975438  

My rule of thumb is "avoid brace initialization unless there is no alternative"... lol.  
  
Thank you for the kind words! I don't know what orbit propagation is, but I hope that the propagations are plentiful.

---

## Comment 9

> Username: grisumbras  
> Created at: 2022-12-03 15:14:32 UTC  
> Url: https://github.com/boostorg/json/issues/816#issuecomment-1336180648  

Btw, I defeat the most vexing parse by using `auto`.
