# #39 - Consider dropping the "dependent typing" term [Closed]

> Username: ldionne  
> Created at: 2015-10-04 01:50:18 UTC  
> Updated at: 2016-04-14 16:31:32 UTC  
> Closed at: 2016-04-12 14:28:36 UTC  
> Url: https://github.com/boostorg/hof/issues/39  

As we discussed at CppCon, it might be a good idea to stop using the term _dependent typing_ in the documentation in favor of another word which is not already used in math/functional programming.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-04-11 18:39:52 UTC  
> Updated at: 2016-04-11 21:27:23 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208491111  

Maybe I should bring this up on the boost mailing lists for a larger consensus. I feel like "dependent typing" describes it quite well, however, I could be wrong.   
  
From wikipedia, it says that a dependent type is a type whose definition depends on a value. This is what `std::integral_constant` does. Furthermore, the result of a function like this would be a dependent type:  
  
``` cpp  
template<class IntegralConstant, class T, class U>  
auto pick(IntegralConstant b, T x, U y)  
{  
    if constexpr(b) return x;  
    else return y;  
}  
```  
  
As the type of the result of the function `pick` is dependent on the value `b`.  
  
Also, a dependent pair could be expressed either as a constraint, which would be an existential quantification:  
  
``` cpp  
// pair of integers where the second is greater than the first  
template<int X, int Y>  
using dpar<X, Y> = typename std::enable_if<(Y > X),   
    std::pair<std::integral_constant<int, X>, std::integral_constant<int, Y>>  
>::type;  
```  
  
Or as a dependence, where `Y` represents how much greater the value is then the first:  
  
``` cpp  
template<int X, int Y>  
using dpar<X, Y> = std::pair<std::integral_constant<int, X>, std::integral_constant<int, X+Y>>;  
```  
  
Perhaps @camio has some insights on this as well.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-04-11 23:13:01 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208608468  

> Furthermore, the result of a function like this would be a dependent type:  
>   
> ``` c++  
> template<class IntegralConstant, class T, class U>  
> auto pick(IntegralConstant b, T x, U y)  
> {  
>     if constexpr(b) return x;  
>     else return y;  
> }  
> ```  
>   
> As the type of the result of the function pick is dependent on the value b.  
  
No, the type of the result is dependent on the **type** of `b`. It would depend on its value if you could write  
  
``` c++  
template<class T, class U>  
auto pick(bool b, T x, U y)  
{  
    if (b) return x;  
    else return y;  
}  
```  
  
But that this is impossible in C++. I think this is the core of the issue, and this is also why I think it would be confusing to use the term "dependent typing". It's almost dependent typing, but not quite. AFAIU, at least.

---

## Comment 3

> Username: camio  
> Created at: 2016-04-12 00:06:21 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208630056  

Interesting conversation!  
  
To clarify, I'll distinguish between C++ types and types at the semantic level. `std::vector`, because it isn't fully saturated, doesn't have a C++ type. It does have a semantic type though, I'd call it Set -> Set. It is a function which takes in a type and returns a type.  
  
What's the semantic type of `std::array` then? I'd say its semantic type is Set -> Int -> Set.  
  
What's the semantic type of `pick` (assuming it is possible in C++17. I'm not sure if it is)?  
  
``` c++  
template<bool b, class T, class U>  
auto pick(T x, U y)  
{  
    if constexpr(b) return x;  
    else return y;  
}  
```  
  
I'd say `(b : Bool) -> (t : Set) -> (u : Set) -> t -> u -> if b then t else u`. That is a dependent type for sure.  
  
Certain C++ code chunks can be though of (semantically) as having dependent types.  
  
I don't know anything about Fit or if the terms are applied correctly or not.  
  
Hope this helps.  
  
-D

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-04-12 00:23:06 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208637067  

> No, the type of the result is dependent on the type of b.  
  
Yes, but the type of `b` is dependent on a value. The fact that it is a polymorphic function shouldn't change the fact that it depends on a value.

---

## Comment 5

> Username: ldionne  
> Created at: 2016-04-12 00:41:16 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208644955  

> Yes, but the type of b is dependent on a value.  
  
But that value must be a compile-time constant, which is equivalent to a type. My point of view is well represented by [this](http://stackoverflow.com/a/9374698/627587) StackOverflow answer (see the end of it).

---

## Comment 6

> Username: ldionne  
> Created at: 2016-04-12 00:44:42 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208647100  

@camio You say  
  
> I'd say `(b : Bool) -> (t : Set) -> (u : Set) -> t -> u -> if b then t else u`. That is a dependent type for sure.  
  
I don't think that's accurate, because if `b` was really `Bool`, we could pass a runtime `Bool` to it, which we can't.

---

## Comment 7

> Username: pfultz2  
> Created at: 2016-04-12 00:46:04 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208647536  

> What's the semantic type of std::array then? I'd say its semantic type is Set -> Int -> Set.  
  
So then the semantic type of `bool_constant` in C++17 would be Bool -> Set, right? And wouldn't that be considered a dependent type?  
  
> I'd say (b : Bool) -> (t : Set) -> (u : Set) -> t -> u -> if b then t else u. That is a dependent type for sure.  
  
Yes, but changing it to a polymorphic function like I defined:  
  
``` cpp  
template<class BoolConstant, class T, class U>  
auto pick(BoolConstant b, T x, U y)  
{  
    if constexpr(b) return x;  
    else return y;  
}  
```  
  
Then the semantic type of `pick` if given a `bool_constant` would be `(b : Bool -> Set) -> (t : Set) -> (u : Set) -> t -> u -> if b then t else u`?  
  
> I don't know anything about Fit or if the terms are applied correctly or not.  
  
This applies to, for example, the [`arg`](http://fit.readthedocs.org/en/latest/arg/index.html) function which pick the Nth argument(similar to `pick` but with integers). So you can write `arg_c<N>(T{}, U{})`, which would be a dependent type, but also `arg(std::integral_constant<int, N>{})(T{}, U{})` can be written, which I would say that `arg` takes a dependent type. A better way to describe it would be that `arg` is a dependent function.  
  
In the documentation for [`if_`](http://fit.readthedocs.org/en/latest/if/index.html), I say " The if_c version can be used to give a boolean condition directly(instead of relying on dependent typing)", which is not the right way to say it at all.  
  
> Hope this helps.  
  
Sure, thanks, for the feedback.

---

## Comment 8

> Username: pfultz2  
> Created at: 2016-04-12 01:10:12 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208652465  

> But that value must be a compile-time constant, which is equivalent to a type. My point of view is well represented by this StackOverflow answer (see the end of it).  
  
I don't see how being statically defined would make it not a dependent type, it would just limit its usage.   
  
> I don't think that's accurate, because if b was really Bool, we could pass a runtime Bool to it, which we can't.  
  
Its still a boolean whether its defined at compile-time or runtime. Furthermore, since this applies to metaprogramming, compile-time is the runtime, so there really isn't a difference.  
  
Otherwise, how should we describe types that depend on values at compile-time? At CppCon, we created the term "value-encoded types", but that is not accurate. The `pick` function doesn't encode the value into the type, actually, the original value is lost and only `T` or `U` remains. Whether its a `T` or `U` depends on what "value-encoded type" is passed to it. Maybe, we could say "value-encoded dependent types", however, I think it is redundant because the only type we will have dependent types is when they are value-encoded or known statically. Therefore, I see no danger in calling it dependent types in C++ even when limited to compile-time values because there is almost no possibility of having run-time dependent typing.  
  
Since this applies to metaprogramming calling it dependently-typed metapgroamming makes it clear that dependent typing only applies to compile-time and not runtime.

---

## Comment 9

> Username: ldionne  
> Created at: 2016-04-12 14:25:40 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208932853  

All I'm saying is that the term "dependent type" specifically refers to a type which depends on a **runtime** value, a feature C++ does not have (because it's limited to compile-time constants, as we both agree on). Using _dependently-typed metaprogramming_ is already better because it hints at compile-time, but I still think that's asking for trouble. The two concepts are both very similar and quite subtle, and I think this terminology will just cause confusion in the long term.  
  
Furthermore, I'm not even sure it's worth introducing a term for this. In your documentation, you could replace  
  
> (instead of relying on dependent typing)  
  
by   
  
> (instead of relying on integral constants)  
  
in all places where you currently use the word "dependent typing" (`fit/if.hpp` and `fit/limit.hpp`). Of course, if you prefer introducing the concept under "dependent typing" or "dependently-typed metaprogramming", I can't really stop you.

---

## Comment 10

> Username: ldionne  
> Created at: 2016-04-12 14:28:36 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208934202  

Closing since I think my original concern has been explained properly, and both points of view can be justified.

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-04-12 14:41:17 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-208939880  

> Furthermore, I'm not even sure it's worth introducing a term for this. In your documentation, you could replace  
  
I agree that would be better in the documentation.

---

## Comment 12

> Username: camio  
> Created at: 2016-04-14 16:31:32 UTC  
> Url: https://github.com/boostorg/hof/issues/39#issuecomment-210036315  

> All I'm saying is that the term "dependent type" specifically refers to a type which depends on a runtime value.  
  
With dependent typing there is no distinction between runtime and compile time. It more refers to the ability to express types which depend on values.
