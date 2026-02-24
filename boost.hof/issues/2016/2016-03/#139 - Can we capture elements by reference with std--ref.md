# #139 - [Adaptors.lazy] Can we capture elements by reference with std::ref? [Closed]

> Username: ldionne  
> Created at: 2016-03-11 20:49:04 UTC  
> Updated at: 2016-03-23 18:21:26 UTC  
> Closed at: 2016-03-22 23:55:00 UTC  
> Url: https://github.com/boostorg/hof/issues/139  

**Note: I tried submitting this issue already, but it appears that GitHub just dropped it. Please ignore if this causes the issue to be submitted twice.**  
  
The reference for `fit::partial` says the following (emphasis mine):  
  
> The `partial` function adaptor allows partial application of the function. If the function can not be called with all the parameters, it will return another function. It will repeatedly do this until the function can finally be called. **By default, `partial` captures all of its variables by value, just like bind. `std::ref` can be used to capture references instead.**  
  
Is it possible to do the same with `fit::lazy`, i.e. is it possible to write:  
  
``` c++  
auto add = [](auto x, auto y) { return x+y; };  
std::string s = "s";  
auto pluralize = fit::lazy(add)(fit::_1, std::ref(s));  
assert(pluralize("toast") == "toasts");  
```  
  
If not, then it would be useful to make it possible. Otherwise, I think this should be documented.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-03-15 20:20:46 UTC  
> Url: https://github.com/boostorg/hof/issues/139#issuecomment-197004919  

> Is it possible to do the same with fit::lazy, i.e. is it possible to write:  
  
Yes this is already possible. I should make it more clear in the docs.
