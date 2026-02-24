# #138 - [Adaptors.lazy] Are the arguments captured by value or by reference? [Closed]

> Username: ldionne  
> Created at: 2016-03-11 20:35:42 UTC  
> Updated at: 2016-03-23 18:22:02 UTC  
> Closed at: 2016-03-22 23:54:45 UTC  
> Url: https://github.com/boostorg/hof/issues/138  

The example for `lazy` says  
  
> ``` c++  
> auto add = [](auto x, auto y) { return x+y; };  
> auto increment = lazy(add)(_1, 1);  
> assert(increment(5) == 6);  
> ```  
  
I am wondering whether `1` in `lazy(add)(_1, 1)` is captured by value (and moved into some object), or if it's captured by reference. Saying that `lazy` is like `std::bind` suggests that it is captured by value, but it would be nice to state this explicitly, like you state explicitly for `fit::partial`.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-11 20:44:59 UTC  
> Url: https://github.com/boostorg/hof/issues/138#issuecomment-195544129  

They do capture by value like `std::bind` and `partial`. I will add a note about that.
