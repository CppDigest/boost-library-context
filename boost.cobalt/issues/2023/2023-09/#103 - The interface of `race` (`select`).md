# #103 - The interface of `race` (`select`) [Open]

> Username: akrzemi1  
> Created at: 2023-09-02 15:59:00 UTC  
> Updated at: 2023-11-09 00:35:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/103  

The interface of `select` is not clearly documented, so it is difficult to figure out all the available signatures and return types. But judging from the rose in the documentation it is non-uniform.  
  
The non-uniform interface of `select()` (spacial-case for all-void) is problematic and may cause unnecessary difficulties and surprises in generic contexts.  
  
```c++  
template <typename T, typename U> // is T a void?  
promise<void> f(promise<T> pt, promise<U> pu)  
{  
  auto r = co_await select(pt, pu);  
  r;  // which interface should I use?  
      // variant or naked size_t?  
}  
```  
  
Same goes for vector:  
  
```c++  
template <typename T> // is T a void?  
promise<void> f(vector<<promise<T>> pv)  
{  
  auto r = co_await select(pv);  
  r;  // which interface should I use?  
      // pair or naked size_t?  
}  
```  
  
Different interface calls for a different name. If `promise<void>` is orders of magnitude more frequent than `promise<anything_else>` then offer name `selcetv` for funcitons returning `variant`/`pair`. also, having two different functions would make it easier to document them.  
  
Second issue. Because the documentation is too scarce, I cannot figure out what is returned by the `select` that is given a zero-sized vector of promises.  
  
Third. Why do you use `boost::variant2` rather than `std::variant`? I would expect a rationale in the docs.
