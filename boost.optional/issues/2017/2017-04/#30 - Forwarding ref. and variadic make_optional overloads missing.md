# #30 - Forwarding ref. and variadic make_optional overloads missing [Closed]

> Username: daniel-j-h  
> Created at: 2017-04-18 14:55:30 UTC  
> Updated at: 2017-05-17 21:12:42 UTC  
> Closed at: 2017-05-17 21:12:42 UTC  
> Url: https://github.com/boostorg/optional/issues/30  

The stdlib provides forwarding ref. overloads and variadic template overloads for `make_optional`:  
http://en.cppreference.com/w/cpp/utility/optional/make_optional  
  
The same is missing from `boost::optional` at least from what I can tell (correct me if I'm wrong).  
  
All I could find [here](https://github.com/boostorg/optional/blob/cb7641dc34bc61cefa44e9bccfebe02889148528/include/boost/optional/optional.hpp#L1279-L1293) are these two overloads:  
  
```c++  
optional<T> make_optional ( T const& v )  
optional<T> make_optional ( bool cond, T const& v )  
```  
  
Which is sub-optimal since now users expecting a move will end up doing a copy instead:  
  
```c++  
boost::make_optional(std::move(bigObject)); // oops, calls T const& overload making a copy  
```  
  
cc @K-Ballo

---

## Comment 1

> Username: akrzemi1  
> Created at: 2017-04-21 07:13:38 UTC  
> Url: https://github.com/boostorg/optional/issues/30#issuecomment-296102825  

Indeed. The rvalue overloads are missing.
