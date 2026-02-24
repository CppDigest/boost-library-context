# #85 - Add a variadic function object [Closed]

> Username: ldionne  
> Created at: 2015-06-07 15:38:00 UTC  
> Updated at: 2015-12-02 22:02:55 UTC  
> Closed at: 2015-12-02 22:02:55 UTC  
> Url: https://github.com/boostorg/hana/issues/85  

Similar to `fit::variadic`. The idea is that sometimes you want interface functions to be variadic but you really need a tuple internally to process the arguments, for example in the `switchAny` example of the quick start. Instead of receiving variadic arguments and then creating a tuple, that could be handled by a combinator:  
  
``` c++  
auto f = variadic([](auto args) { ... });  
f(1, 2, 3, 4); // args = [1, 2, 3, 4]  
```

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-06-07 21:53:15 UTC  
> Url: https://github.com/boostorg/hana/issues/85#issuecomment-109802670  

Interestingly enough, I am planning to deprecate `fit::variadic` since it can simply be accomplished with composition:  
  
``` cpp  
auto f = compose([](auto args) { ... }, make_tuple);  
```  
  
I am also adding [`fit::flow`](https://github.com/pfultz2/Fit/blob/flow/fit/flow.h) which does reverse composition, so the lambda can be put last, which is nicer when the lambda is multiline:  
  
``` cpp  
auto f = flow(make_tuple, [](auto args)   
{   
    ...   
});  
```  
  
Plus this is more flexible, since the user can define what kind of tuple they want created.   
  
Also, I don't think its necessary most of the time. If `switch_` required the `default_` to be at the end, then it could be written like this using the Fit and Tick library:  
  
``` cpp  
template <typename Any>  
auto switch_(Any& a) {  
  return [&a](auto... cases_) {  
    auto&& last = args(std::integral_constant<int, sizeof...(cases_)>())(cases_...);  
    static_assert(last.first == type<_default>, "Default case should be last");  
  
    fit::by(fit::conditional(  
      [&](auto case_, TICK_PARAM_REQUIRES(case_.first != type<_default>)) {  
        using T = typename decltype(case_.first)::type;  
        if (a.type() == typeid(T)) case_.second(*boost::unsafe_any_cast<T>(&a));  
      },  
      [&](auto case_) {  
        case_.second();  
      }  
    ))(cases_...);  
  }  
}  
```  
  
Of course, I haven't compiled it yet, so it may not work.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-07 22:09:22 UTC  
> Url: https://github.com/boostorg/hana/issues/85#issuecomment-109805825  

The problem with the code you posted is that you don't stop at the first case you find. So you'll be comparing `a.type() == typeid(T)` for all types `T` in the `switch`, even if you already found the matching one. If I understand correctly, of course. Anyway, regarding `compose`: your `compose` then isn't the same as Hana's `compose`, which only composes the first argument. Your `compose` is equivalent to [`demux`](http://ldionne.com/hana/group__group-functional.html#ga3a8316acd5efa22c3d1861b62d5df3c2) in Hana:  
  
``` c++  
compose(f, g)(x1, ..., xn) == f(g(x1), x2, ..., xn)  
demux(f)(g)(x1, ..., xn) == f(g(x1, ..., xn)) // equivalent to your compose  
```  
  
The reason why Hana's `compose` only composes the first argument was initially because of the Haskell language. In Haskell, all functions are unary functions because of currying. If you curried functions in Hana and used `compose` with them, you could translate Haskell code to C++ without even thinking. This is obviously an byproduct of Hana's beginnings as a functional library and it would be worth reevaluating what is gained/lost by having `compose` only handle the first argument.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-06-07 22:53:15 UTC  
> Updated at: 2015-06-07 22:54:25 UTC  
> Url: https://github.com/boostorg/hana/issues/85#issuecomment-109809934  

> The problem with the code you posted is that you don't stop at the first case you find. So you'll be comparing a.type() == typeid(T) for all types T in the switch, even if you already found the matching one. If I understand correctly, of course.  
  
Thats true. I forgot about that aspect. Well instead of breaking out into a separate function, a lambda with a fix point combinator could be used:  
  
``` cpp  
template <typename Any>  
auto switch_(Any& a) {  
  return [&a](auto ...cases_) {  
    auto cases = make_tuple(cases_...);  
    auto default_ = find_if(cases, [](auto const& c) {  
      return c.first == type<_default>;  
    });  
    static_assert(default_ != nothing,  
      "switch is missing a default_ case");  
    auto rest = filter(cases, [](auto const& c) {  
      return c.first != type<_default>;  
    });  
    return unpack(rest, fit::fix(fit::match(  
      [&](auto self, auto& case_, auto& ...rest) {  
        using T = typename decltype(case_.first)::type;  
        return t == typeid(T) ? case_.second(*boost::unsafe_any_cast<T>(&a))  
                              : self(rest...);  
      },  
      [&](auto) { return default_->second(); }  
    )));  
  };  
}  
```  
  
> This is obviously an byproduct of Hana's beginnings as a functional library and it would be worth reevaluating what is gained/lost by having compose only handle the first argument.  
  
Does Hana rely on that behavior?

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-08 18:55:41 UTC  
> Url: https://github.com/boostorg/hana/issues/85#issuecomment-110107137  

> This is obviously an byproduct of Hana's beginnings as a functional library and it would be worth reevaluating what is gained/lost by having compose only handle the first argument.  
> Does Hana rely on that behavior?  
  
I think it might in some places in the implementation, but these places should probably be refactored to improve their compile-time anyway. I don't think that's a major problem, but I'll obviously have to check before I switch to Fit (if that happens). Right now I'm running for the formal review, so it would be better that we postpone any kind of large change to after the review.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-12-02 22:02:55 UTC  
> Url: https://github.com/boostorg/hana/issues/85#issuecomment-161447775  

For the same reason I closed #60, I won't add this to Hana. I want to reduce the prominence of the Functional module.
