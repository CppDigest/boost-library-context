# #21 - Find a way to make Logical's methods short-circuiting [Closed]

> Username: ldionne  
> Created at: 2015-03-22 16:23:28 UTC  
> Updated at: 2017-04-05 03:08:39 UTC  
> Closed at: 2017-04-05 03:08:38 UTC  
> Url: https://github.com/boostorg/hana/issues/21  

This requires having a proper way to handle lazy boolean expressions, which is not exactly trivial.

---

## Comment 1

> Username: compiletime  
> Created at: 2017-04-04 16:48:16 UTC  
> Url: https://github.com/boostorg/hana/issues/21#issuecomment-291561446  

Here is a rough draft of short-circuiting a template instantiation:  
https://gist.github.com/compiletime/5c30bc18f22582cbd9e9ebb3847776d6  
  
@ldionne If this looks like the right approach to you, would you assign this issue to me?

---

## Comment 2

> Username: ldionne  
> Created at: 2017-04-04 16:55:37 UTC  
> Url: https://github.com/boostorg/hana/issues/21#issuecomment-291563649  

Thanks for looking at this. However, let me clarify what I meant when I opened this issue. What I meant was that it _should_ be possible for the following to only ever instantiate the left hand side:  
  
```c++  
hana::true_c || hana::any_of(some-tuple, hana::equal.to(hana::type<int>{}))  
```  
  
In other words, I wanted the logical operators to have short-circuitting instantiation, just like they have short-circuitting evaluation at runtime. However, the problem is that in order to even resolve which `operator||` should be called, both argument types obviously need to be instantiated. This leaves us with the only option of returning some lazy compile-time boolean from `hana::any_of`, so that `operator||` only evaluates it when needed. However, this means that all the algorithms that return booleans would need to return that lazy boolean, and it would also require some level of interoperability with normal `integral_constant`s. I'm not sure this can be achieved at all, frankly.

---

## Comment 3

> Username: compiletime  
> Created at: 2017-04-05 03:06:49 UTC  
> Url: https://github.com/boostorg/hana/issues/21#issuecomment-291733697  

Your intention is very clear now. I mistook it for short-circuiting the concept check.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-04-05 03:08:38 UTC  
> Url: https://github.com/boostorg/hana/issues/21#issuecomment-291734249  

Sorry the issue was unclear at first. This was opened a long time ago when I was the only one looking at this :-). Anyway, I'll close this as I think this can't be implemented reasonably, and `if constexpr` in C++17 will make some of this a non-issue (since you can nest `if`s instead of relying on logical short-circuiting).
