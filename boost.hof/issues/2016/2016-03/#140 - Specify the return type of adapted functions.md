# #140 - [Doc] Specify the return type of adapted functions [Open]

> Username: ldionne  
> Created at: 2016-03-11 21:01:16 UTC  
> Updated at: 2016-03-12 08:30:14 UTC  
> Url: https://github.com/boostorg/hof/issues/140  

I'm looking at `flip` right now, but this also applies to basically all the rest of the library. The documentation says  
  
> **Synopsis**  
>   
> ``` c++  
> template<class F>  
> flip_adaptor<F> flip(F f);  
> ```  
>   
> **Semantics**  
>   
> ``` c++  
> assert(flip(f)(x, y, xs...) == f(y, x, xs...));  
> ```  
  
However, it would be nice to know what is the return type of the `operator()` of the `flip_adaptor`. In particular, I'm thinking about `auto` vs `decltype(auto)` for the return type. This is important, because it changes the expected behaviour of functions that return references, for example:  
  
``` c++  
std::string s = "foobar";  
auto f = [&](auto const& ...) -> std::string& { return s; };  
auto g = fit::flip(f);  
  
std::string const& ref_to_s = g("don't", "care"); // is this holding a reference to `s`, or a (dangling) reference to a temporary created by `g`?  
```  
  
I understand this might present a challenge in terms of how to document this. If you want, you can take a look at how Hana specifies the behaviour of its functions using pseudo-code and lambdas, for example [here](http://boostorg.github.io/hana/group__group-functional.html#ga004f884cdbb85c2efe3383c1db450094).
