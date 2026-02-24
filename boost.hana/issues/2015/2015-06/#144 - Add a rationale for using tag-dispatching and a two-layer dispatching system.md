# #144 - Add a rationale for using tag-dispatching and a two-layer dispatching system [Closed]

> Username: ldionne  
> Created at: 2015-06-20 21:25:36 UTC  
> Updated at: 2015-06-21 16:45:12 UTC  
> Closed at: 2015-06-20 21:48:57 UTC  
> Url: https://github.com/boostorg/hana/issues/144  



---

## Comment 1

> Username: viboes  
> Created at: 2015-06-21 05:32:48 UTC  
> Updated at: 2015-06-21 05:40:43 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113865305  

In order to compare, you should state clearly with which other techniques you are comparing.  
The first : two layer respect to one layer. Eric's blog give the same argument. I would add here some examples of what has been done in this second layer so that the benefit is yet more evident.  
  
I believe that the second is explaining why you use a trait models_impl instead of checking valid expressions, not why you need Tags. This is IMO a consequence of making the mapping explicit, you let the responsibility on the user side. Explicit mapping has also the advantage that the user is adhering to the semantic laws.  
  
You need yet to explain why you use an intermediary Tag to do this mapping instead of the type itself.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-21 05:39:57 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113865440  

You should also explain the down side of no checking the valid expressions. When the user do an invalid mapping (the syntactical expression are not correct), the error would be found in a deeper stack than if checked on the `models` meta-function.

---

## Comment 3

> Username: viboes  
> Created at: 2015-06-21 05:53:46 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113865709  

BTW, high order functions can be defined on top of functions, so this doesn't justify the two layers rationale. We can define a `begin_fn` using the current `std::begin`. It is a shame that we don't know how to build in C++ a hof from a function, such as e.g. `hof<std::begin>`.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-21 13:44:03 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113902853  

> It is a shame that we don't know how to build in C++ a hof from a function, such as e.g. hofstd::begin.  
  
See [FIT_LIFT](https://github.com/pfultz2/Fit/blob/08ea9bfc999cb045bcf91a1fdbbe00eb46e76598/fit/lift.h) from the Fit library.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-21 14:03:52 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113904137  

> You should also explain the down side of no checking the valid expressions. When the user do an invalid mapping (the syntactical expression are not correct), the error would be found in a deeper stack than if checked on the models meta-function.  
  
The interface functions already make sure that you model the proper concepts, and otherwise it fails at the top level.

---

## Comment 6

> Username: viboes  
> Created at: 2015-06-21 14:16:43 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113907257  

I see the following kind of trait declaration  
  
``` c++  
    template <typename M>  
    struct models_impl<Monad, M>  
        : _integral_constant<bool,  
            !is_default<flatten_impl<M>>{}() ||  
            !is_default<chain_impl<M>>{}()  
        >  
    { };  
```  
  
How this check the syntactical constraint?

---

## Comment 7

> Username: viboes  
> Created at: 2015-06-21 14:24:42 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113907819  

Of course, macros can help here, but I was asking for a C++ (no preprocessor solution). I've this rule: when you need to use the preprocessor, it is either a sign that there is something missing in the language or my abstraction is not a good one.  
  
Anyway, thanks for the link. BTW, the Fit FIT_LIFT_CLASS  macro needs to instantiate the class.

---

## Comment 8

> Username: viboes  
> Created at: 2015-06-21 14:36:22 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113909655  

BTW, what is `Chain`in https://github.com/ldionne/hana/blob/master/include/boost/hana/fwd/monad.hpp  
  
``` c++  
 return Chain::apply(static_cast<Xs&&>(xs), static_cast<F&&>(f));  
```

---

## Comment 9

> Username: ldionne  
> Created at: 2015-06-21 14:55:51 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113911353  

> I see the following kind of trait declaration  
>   
> [...]  
>   
> How this check the syntactical constraint?  
  
This just checks that either `chain_impl<M>` or `flatten_impl<M>` are implemented. We have to rely on the user providing a valid definition of the nested `apply`, but we can't check for that because that would execute the algorithm.

---

## Comment 10

> Username: ldionne  
> Created at: 2015-06-21 14:57:31 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113911446  

`Chain` is `chain_impl<M>` if `M` is a `Monad`, and a struct with a deleted `apply` static function otherwise. This prevents the compiler from generating long error messages when a constraint is not respected.

---

## Comment 11

> Username: ldionne  
> Created at: 2015-06-21 14:58:09 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113911467  

See [this](https://github.com/ldionne/hana/blob/72c32071351abbeb20cb2db2ba1c5785c3159a92/include/boost/hana/fwd/monad.hpp#L265) and then [this](https://github.com/ldionne/hana/blob/72c32071351abbeb20cb2db2ba1c5785c3159a92/include/boost/hana/detail/dispatch_if.hpp#L41).

---

## Comment 12

> Username: viboes  
> Created at: 2015-06-21 16:38:48 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113925598  

Oh sorry, how I missed the definition of Chain :(.  
  
This is why I said that you are not checking the syntax validity. Is this one of the factors that make Hana compile quicker?

---

## Comment 13

> Username: ldionne  
> Created at: 2015-06-21 16:45:12 UTC  
> Url: https://github.com/boostorg/hana/issues/144#issuecomment-113926922  

> This is why I said that you are not checking the syntax validity. Is this one of the factors that make Hana compile quicker?  
  
You mean the conditional checking of the concept requirements only when `BOOST_HANA_CONFIG_DISABLE_CONCEPT_CHECKS` is not defined? If so, requirements are checked by default and I only see a very small speedup (if any) for the whole test suite when the checks are disabled. So I don't think it plays a significant role in making the compile-times better.
