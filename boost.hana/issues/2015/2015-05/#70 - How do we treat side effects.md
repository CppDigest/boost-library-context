# #70 - How do we treat side effects? [Closed]

> Username: ldionne  
> Created at: 2015-05-04 17:59:40 UTC  
> Updated at: 2015-05-28 23:28:32 UTC  
> Closed at: 2015-05-28 23:28:32 UTC  
> Url: https://github.com/boostorg/hana/issues/70  

Technically, all the concepts used by Hana require the functions to be pure. Should we really require this, or should we encourage people to use pure functions without actually saying "impure functions will cause undefined behaviour"?  
  
There are some points in favour of using pure functions:  
1. Easier to reason about  
2. Most of the time, functions are not guaranteed to be called in any specific order. This is made even more real by the fact that expanding a parameter pack when calling a function does not guarantee any order of the evaluation of the arguments.  
3. We do not guarantee the number of times a function is called. Hence, if a function has side effects, it might be called any number of times. If we allow side effects, we must also document how many times a function is called when used with some algorithm.  
4. We can implement some nice optimizations for predicates that return `Constant`s if we assume they do not have side effects.  
  
There is at least one point in favour of side effects:  
1. The `for_each` function is currently handled by `Foldable`. This is easy and quite intuitive, but it is incorrect strictly speaking. If we require pure functions, `for_each` should be handled through some kind of monadic fold, which is much more complex.

---

## Comment 1

> Username: vendethiel  
> Created at: 2015-05-04 18:12:49 UTC  
> Url: https://github.com/boostorg/hana/issues/70#issuecomment-98801738  

In my head, "template metaprogramming" means "pure" :-). I think it's a nice property it has... I'm not sure I get the argument for side effects?

---

## Comment 2

> Username: ldionne  
> Created at: 2015-05-04 18:21:10 UTC  
> Url: https://github.com/boostorg/hana/issues/70#issuecomment-98803321  

The argument is that `for_each` currently takes a function ` f` and a `Foldable`, and applies `f` to each element of the `Foldable`. If we only allow pure functions, `f` can't perform any side effect and calling `for_each` with `f` is equivalent to doing nothing. To handle side effects while remaining pure, we would have to use `Monad`s like they do in Haskell. However, we're in C++ and I kinda think one should be able to call `for_each` with a side-effecting function without worrying about monads.

---

## Comment 3

> Username: vendethiel  
> Created at: 2015-05-04 19:19:08 UTC  
> Url: https://github.com/boostorg/hana/issues/70#issuecomment-98822435  

Right, I see your point now. I thought `for_each` did collect the results (in some way:).  
  
I'm afraid side effects "there" would invalidate a whole bunch of guarantees you can currently have using hana, no?

---

## Comment 4

> Username: ldionne  
> Created at: 2015-05-04 20:52:22 UTC  
> Url: https://github.com/boostorg/hana/issues/70#issuecomment-98846725  

No, `transform` (in `Functor`) collects the result. Side effects in `for_each` would not invalidate any guarantees. If you look at the documentation for [`for_each`](http://ldionne.com/hana/structboost_1_1hana_1_1Foldable.html#a80176fbdfbccc09e902263557eb0984d), side effects are clearly expected, and the implementation will work just as expected.  
  
The problem is that we can't provide those guarantees while being formally correct. More precisely, the concept hierarchy used in Hana is based on category theory (similar to Haskell's). However, when defining the Hana category, we must restrict ourselves to pure functions that terminate for the construction to be correct. Hence, impure functions are not even part of Hana's universe, so providing guarantees about them in `for_each` does not have a proper formal meaning. This is annoying, because giving `for_each` a proper formal meaning would mean making its requirements more complex (e.g. it should be given a monadic function), and then we should introduce a `State` monad, and so on.  
  
I think the question boils down to: Is is morally correct to be "fast and loose" regarding the formal interpretation of some functions (like `for_each`) in favour of more usability and simplicity on the user side? I'd be tempted to say yes, but this whole philosophy should be documented and those functions that break the formalism should be clearly documented as such.  
  
Also, note that this "philosophical" question is related to e.g. the inability to define the signature of the `if_` function. Technically, the signature of `if_` should be something like  
  
```  
if_ : Boolean x T x T -> T  
```  
  
However, in the context of heterogeneous programming, we actually want it to be more like:  
  
```  
if_ : Boolean x T x U -> T or U  
```  
  
There are many ways to reconcile both:  
1. Consider `if_ : Boolean x T x U -> CommonType(T, U)`  
2. Consider `if_ : Boolean x T x U -> Variant<T, U>`  
  
However, none of them gives us the ease of use that the current `if_` provides, even though the current `if_` is formally incorrect.
