# #9 - Rename Monad::bind [Closed]

> Username: ldionne  
> Created at: 2014-10-05 00:17:10 UTC  
> Updated at: 2015-04-19 19:57:40 UTC  
> Closed at: 2015-04-19 19:57:40 UTC  
> Url: https://github.com/boostorg/hana/issues/9  

The name is misleading because of the existence of `std::bind`. If anyone can come up with a better name, please suggest it.

---

## Comment 1

> Username: viboes  
> Created at: 2014-10-05 11:12:12 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-57932932  

Yes, there are at least 3 major differences  
1 the order of parameters  
2 monad::bind calls the continuation while std::bind create a functor.  
  
`std::experimental::future<T>` has `then()``, but the continuation takes the``future<T>``  as parameter and it has an asynchronous connotation.  
  
I suggested the addition of `next` with  a continuation taking  `T`but it also has an asynchronous connotation.  
  
`apply` seems better, but the order of parameters should be changed. This name is also appropriated as a replacement for `fmap`.  
  
What do you think of overloading `apply` to cover `fmap`and `bind`?  
  
```  
apply :: (a -> b) -> f a -> f b  
apply :: (a -> m b) -> m a -> m b  
```

---

## Comment 2

> Username: ldionne  
> Created at: 2014-10-05 13:40:14 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-57936319  

We totally agree that `bind` is not the right name. However, `apply` is already used to denote function application (see boost/hana/functional/apply.hpp).  
  
Also, I do not think `fmap` should be renamed to `apply`; I think the intent is much more clearly expressed by saying `fmap` (`map`ping a function), and that name (or close variations thereof) is already used in other programming languages like Python.  
  
That being said, I am not closed to the idea of renaming `bind` to `apply`, and then finding a new name for `apply`. Or even better: if we could come up with a data type for functions, and if that data type was the instance of a type class which provided a method equivalent to `apply`, then I would happily use that method instead of `apply` and we could rename `bind` to apply. That option might even make us able to generalize (and hopefully rename) some other functions from the Functional module like `demux`.  
  
As for the idea of overloading the name `apply`, I am reluctant to do so at first sight. I prefer to keep things more explicit (_especially_ in the case of such similar-looking functions) to avoid confusion. Keeping things explicit forces one to stay honest and keep track of what is inside a `Functor` and what isn't in a more rigorous way.

---

## Comment 3

> Username: wolftype  
> Created at: 2014-10-08 16:43:35 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-58387995  

extend ?  
pipe (cause of | )  
fjoin ( that's ugly)  
  
Nice work on this Louis!

---

## Comment 4

> Username: ldionne  
> Created at: 2014-10-08 23:42:17 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-58445215  

`extend` is already commonly used for [Comonads](http://hackage.haskell.org/package/comonad-1.1.0/docs/Control-Comonad.html). Since I think there's a nice generalization of the current `Logical` type class using Comonads, I might need that name later on. Otherwise, it might make sense to use `extend`; I'll consider that.  
  
`pipe` might make sense too. I was reluctant at first sight, but it really might make sense.  
  
`fjoin` is too ugly.  
  
Thanks for the suggestions!

---

## Comment 5

> Username: pfultz2  
> Created at: 2014-11-11 06:54:32 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-62509349  

Actually, I implemented the state monad in C++, just as an exercise(see [here](https://gist.github.com/pfultz2/f538423ebc9bbbe3df92)). I used `for_` for `bind` and `yield` for `return` so there is no double return at the end. I also had a `do_` for the `then` operator. It seems those terms would be more familiar to C++ programmers. I chose `for_` since it introduces a new scope and variables, it seemed fitting. Just my two cents.

---

## Comment 6

> Username: ldionne  
> Created at: 2014-11-11 19:34:17 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-62604824  

That's a very interesting gist. I'll have a deeper look at it when I get the time (exams right now), but I think this might be a nice direction to take.

---

## Comment 7

> Username: ldionne  
> Created at: 2015-04-01 14:44:32 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88510486  

Two new suggestions:  
  
``` cpp  
chain(monadic_result, monadic_function)  
monadic_apply(monadic_function, monadic_result)  
```

---

## Comment 8

> Username: ldionne  
> Created at: 2015-04-01 14:47:00 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88511093  

Note that `monadic_apply` would be consistent with the recently added `monadic_fold`. I still don't like it as much as `chain` (or `pipe`) though.

---

## Comment 9

> Username: pfultz2  
> Created at: 2015-04-01 15:23:06 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88521792  

Is it possible to combine `fold` with an overload for `monadic_fold`? Or perhaps there is a way to annotate a function to signify that it returns a monadic type?

---

## Comment 10

> Username: ldionne  
> Created at: 2015-04-01 18:36:42 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88589123  

You mean provide only `fold` and use a monadic variant whenever the function returns a monadic result? This is not possible right now, because we don't know the generalized type returned by functions. It _would_ be possible to annotate functions so we have that knowledge, e.g.:  
  
``` cpp  
auto f = monadic_function<Monad>([](auto x) { ... });  
fold(xs, state, f); // uses a monadic fold  
```  
  
Is this what you mean?

---

## Comment 11

> Username: pfultz2  
> Created at: 2015-04-01 18:48:40 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88592388  

> Is this what you mean?  
  
Yes. Also, I wonder if `Monad` could be deduced at a later stage. So essentially you could write:  
  
``` cpp  
auto f = monadic_function([](auto x) { ... });  
fold(xs, state, f); // uses a monadic fold  
```

---

## Comment 12

> Username: ldionne  
> Created at: 2015-04-01 20:18:58 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88619903  

I assume you mean that `Monad` would be deduced when we call `monadic_function(f)` on an argument. This way, it could deduce it from the return type of the wrapped function `f`. Is this what you mean?  
  
If so, this is a brilliant idea, but unfortunately it won't work. `monadic_fold` must know the `Monad` even when the function is never called, for example in the case of an empty sequence. This is required so it can `lift` the `state` into the proper Monad.  
  
It _would_ be possible to wrap the state in a "yet-to-be-determined" Monad, by using e.g. `lift(state)` instead of `lift<M>(state)`. I'm not exactly sure what that would buy us and what it might complicate, but I would say it is worth thinking about.  
  
I personnally like the fact that `monadic_fold` must be written explicitly, since they really are two different things. If we can find a nice way (e.g. a proper generalization of folding and monadic folding) to make `fold` and `monadic_fold` coincide then I would be interested, but otherwise I don't see this as a defect.

---

## Comment 13

> Username: pfultz2  
> Created at: 2015-04-01 21:01:05 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88630760  

> but unfortunately it won't work. monadic_fold must know the Monad even when the function is never called  
  
Oh yea thats right.  
  
> I personnally like the fact that monadic_fold must be written explicitly, since they really are two different things  
  
Yes it does make sense to have two seperate functions, as well. I was just trying to think of a possible different approach.

---

## Comment 14

> Username: viboes  
> Created at: 2015-04-02 05:47:22 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88748844  

An alternative to renaming to avoid syntactic or semantic conflicts with other names is, in C++, to use namespaces to name type classes.   
  
```  
auto x = monad::bind(m, f);  
auto y = functor::map(f, m);  
  
```  
  
As Haskell as no namespaces, it needs to use prefix `f`, `m`  such as in `fmap`, `mzero`, `mplus`.   
  
I'm not saying that bind and map are the best names, just that adding the type class give us more freedom.

---

## Comment 15

> Username: ldionne  
> Created at: 2015-04-02 13:19:32 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-88893327  

While it technically gives us more freedom, in practice we then have to type `monad::bind` all around or `using namespace boost::hana::monad`. I find this unattractive. Also, when you push the concept, you end up with `foldable::fold.left`, `sequence::remove_at`, `searchable::find_if` and so on, which is cumbersome. We don't have major naming conflicts in the library right now as I've managed to name things in such a way that everything fits. Of course, the names are not perfect (as reflected by this thread), but I don't have `fmap` and `mzero` and so on either. I'd rather not use namespaces.

---

## Comment 16

> Username: ldionne  
> Created at: 2015-04-19 17:18:53 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-94298321  

I'm about to close this. I decided to rename `bind` to `chain`. I also want to thank @pfultz2  for suggesting the `monadic_function` thing; this made me realize that I could remove template parameters in some cases by deferring the computation, which I just did with `mcompose` (now called `monadic_compose` by the way).

---

## Comment 17

> Username: ldionne  
> Created at: 2015-04-19 19:57:40 UTC  
> Url: https://github.com/boostorg/hana/issues/9#issuecomment-94309090  

fixed by fb518d859afd9fd4fd0e4a4b6c1fc826fd7ac1ae
