# #321 - Strong data types [Closed]

> Username: ldionne  
> Created at: 2017-02-18 20:40:43 UTC  
> Updated at: 2017-02-25 03:06:51 UTC  
> Closed at: 2017-02-18 20:40:47 UTC  
> Url: https://github.com/boostorg/hana/issues/321  

I am opening this issue just because I want to dump some information I have in my head and remove the `experimental/strong_datatypes.cpp` experiment without the idea behind it being lost forever. I'll close the issue right after opening it.  
  
Currently, higher order concepts in Hana are very fuzzy. Indeed, if you look at e.g. `Functor`, it is currently just a normal concept like `Monoid` is. However, in FP, `Functor` is a concept that takes another type to operate on (as in `Functor<T>`). The whole point of Hana is that the "types" of objects are not their real C++ type anymore, it's their tag. For example, you don't have a `Functor<integral_constant<int, 3>>` (which is incredibly boring), but you have a `Functor<integral_constant_tag<int>>`. Then, the functor can hold objects with different types (thus encoding `integral_constant`s with different values), but the "type" of these objects (which I called _generalized type_ in my thesis) is their tag. That creates another level of type-system on top of the usual C++ type system, which allows us to manipulate heterogeneous objects with some level of formalism (Functors, Monads, etc.).  
  
That being said, Hana does not actually enforce this formalism, mainly for two reasons. First of all, it would greatly reduce the usefulness of the library, since the goal really is to setup a framework that one can think with, without necessarily being very strict about it. In reality, we often want to "abuse" Hana's type system and put stuff that do not have anything in common (so of different generalized types) inside a tuple. We want it to just work, and it does but (technically) it breaks the formalism.  
  
Secondly, it would be very painful to implement. Tag-dispatching is already very ad-hoc, and having proper higher-order type dispatching would be a huge pain in the ass to implement and work with. Nonetheless, for the sake of correctness, I had made an experiment to check how higher-order tag dispatching might work and how we could define e.g. `Applicative` over a specific tag. Here's the experiment:  
  
```c++  
// Copyright Louis Dionne 2013-2017  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
  
#include <boost/hana/ap.hpp>  
#include <boost/hana/config.hpp>  
#include <boost/hana/core/make.hpp>  
#include <boost/hana/core/tag_of.hpp>  
#include <boost/hana/tuple.hpp>  
namespace hana = boost::hana;  
  
  
namespace wrap_detail {  
    template <typename Datatype, typename X>  
    struct wrapper {  
        X unwrap;  
        using hana_tag = Datatype;  
    };  
  
    template <typename Datatype>  
    struct wrap_impl {  
        template <typename X>  
        constexpr auto operator()(X x) const {  
            return wrapper<Datatype, X>{x};  
        }  
    };  
}  
  
template <typename Datatype>  
constexpr wrap_detail::wrap_impl<Datatype> wrap{};  
  
BOOST_HANA_CONSTEXPR_LAMBDA auto unwrap = [](auto x) {  
    return x.unwrap;  
};  
  
//////////////////////////////////////////////////////////////////////////////  
// core  
//////////////////////////////////////////////////////////////////////////////  
template <typename ...>  
struct not_implemented;  
  
  
//////////////////////////////////////////////////////////////////////////////  
// Functor  
//////////////////////////////////////////////////////////////////////////////  
template <typename X, typename F, typename Enable = void>  
not_implemented<X, F> fmap_impl{};  
  
auto fmap = [](auto x, auto f) {  
    return fmap_impl<  
        hana::tag_of_t<decltype(x)>,  
        hana::tag_of_t<decltype(f)>  
    >(x, f);  
};  
  
  
//////////////////////////////////////////////////////////////////////////////  
// Applicative  
//////////////////////////////////////////////////////////////////////////////  
template <typename F, typename X, typename Enable = void>  
not_implemented<F, X> ap_impl{};  
  
auto ap = [](auto f, auto x) {  
    return ap_impl<  
        hana::tag_of_t<decltype(f)>,  
        hana::tag_of_t<decltype(x)>  
    >(f, x);  
};  
  
template <typename A, typename Enable = void>  
not_implemented<A> lift_impl{};  
  
template <template <typename> class A>  
auto lift = [](auto x) {  
    return lift_impl<A<hana::tag_of_t<decltype(x)>>>(x);  
};  
  
  
//////////////////////////////////////////////////////////////////////////////  
// Function  
//////////////////////////////////////////////////////////////////////////////  
template <typename F, typename X, typename Enable = void>  
not_implemented<F, X> apply_impl{};  
  
auto apply = [](auto f, auto x) {  
    return apply_impl<  
        hana::tag_of_t<decltype(f)>,  
        hana::tag_of_t<decltype(x)>  
    >(f, x);  
};  
  
template <typename Domain, typename Codomain>  
struct Function;  
  
template <typename Domain, typename Codomain>  
auto function = [](auto f) {  
    return wrap<Function<Domain, Codomain>>(f);  
};  
  
template <typename X, typename Y>  
auto apply_impl<Function<X, Y>, X> = [](auto f, auto x) {  
    return unwrap(f)(x);  
};  
  
  
//////////////////////////////////////////////////////////////////////////////  
// List  
//////////////////////////////////////////////////////////////////////////////  
template <typename T>  
struct List;  
  
template <typename T>  
auto list = [](auto ...xs) {  
    return wrap<List<T>>(  
        [=](auto f) { return f(xs...); }  
    );  
};  
  
template <typename X, typename Y>  
auto fmap_impl<List<X>, Function<X, Y>> = [](auto xs, auto f) {  
    return unwrap(xs)([=](auto ...xs) {  
        return list<Y>(apply(f, xs)...);  
    });  
};  
  
template <typename X>  
auto lift_impl<List<X>> = [](auto x) {  
    return list<X>(x);  
};  
  
template <typename X, typename Y>  
auto ap_impl<List<Function<X, Y>>, List<X>> = [](auto fs, auto xs) {  
    auto hana_fs = unwrap(fs)([](auto ...fs) {  
        return hana::make_tuple(hana::partial(apply, fs)...);  
    });  
    auto hana_xs = unwrap(xs)(hana::make_tuple);  
    auto hana_result = hana::ap(hana_fs, hana_xs);  
  
    return hana::unpack(hana_result, list<Y>);  
};  
  
  
//////////////////////////////////////////////////////////////////////////////  
// Any (that's a compile-time std::any, which is trivial to implement in Hana world)  
//////////////////////////////////////////////////////////////////////////////  
struct Any;  
  
auto any = [](auto x) {  
    return wrap<Any>(x);  
};  
  
  
int main() {  
    auto f = function<int, int>([](auto x) { return x + 1; });  
    auto xs = list<int>(1, 2, 3, 4);  
    fmap(xs, f);  
  
    lift<List>(2);  
    ap(list<Function<int, int>>(f, f), list<int>(1, 2));  
  
    auto g = function<Any, int>([](auto /*x*/) {  
        // We can't do anything with an Any, so there's not much choice here.  
        return 1;  
    });  
    fmap(list<Any>(any(1), any('2'), any("345")), g);  
}  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-02-18 20:57:58 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-280874492  

@ldionne Is there anything you regret about the current tag system?  
  
The mechanisms you've provided in Boost.Hana have become my preferred method for abstraction.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-02-18 21:01:44 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-280874745  

I'm glad you like it. If I have one regret, it would be that I allowed overriding every single function, when I think it would have been sufficient and lighter to just allow specific customization points. I mean, why would you override `replace_if` when you can just implement `transform`? The initial goal was to provide full flexibility for funky data structures, but I think that wasn't necessary and the price we pay for that is tag dispatching on every single Hana function call, which is a bit heavy.  
  
Also, I think this whole tag-dispatching idea could be replaced by C++0x concepts (which provided concept maps), but that ship has sailed. So I guess we're left to implement those however we can.

---

## Comment 3

> Username: brunocodutra  
> Created at: 2017-02-18 23:44:43 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-280883747  

Have you ever considered using ADL as a customization point instead of tag dispatching, something in the lines of [this paper](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4381.html) by Eric Niebler?  
  
The only disadvantage I see to ADL is that it makes it troublesome for the user to override functions such as `make_tuple` that don't necessarily operate on user defined types, but I wonder if you see other issues as well?

---

## Comment 4

> Username: ldionne  
> Created at: 2017-02-18 23:57:08 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-280884330  

I have considered it, somewhat. I guess the main reason why I did not go with ADL is that it's harder to define models for types that you don't control. You have to define the functions in an associated namespace of the class, which is not always possible.  
  
Another problem I see (maybe?) is with providing default implementations in concepts. Right now, we're able to introspect and check whether a type defines some operation. If not, then we can provide a default one. If we were to use ADL, we'd need to check whether the function call compiles using SFINAE, but whether the function call compiles depends on whether there's a default definition! We need something else to break this loop, and that's what `hana::is_default` and `hana::default_` achieve.  
  
I think I was also scared that overload resolution and ADL would be expensive, but I'm not sure whether that's a reasonable concern. I think it had some weight in the balance though.  
  
Finally, I think the most important reason is that getting everything to work as it does (being able to provide default definitions, and even multiple sets of minimal complete definitions) was incredibly difficult. I remember it as being possibly the most frustrating and difficult part of all my work on Hana. It took weeks, if not months, and I tried so many things that never quite worked for subtle reasons. When I finally got it to work, I just went with it even though it was not perfect.

---

## Comment 5

> Username: brunocodutra  
> Created at: 2017-02-19 01:03:23 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-280887141  

> You have to define the functions in an associated namespace of the class, which is not always possible.  
  
But isn't the user usually interested in overriding algorithms precisely for types he introduces himself? Even if not, it seems to me that a wrapper type should do the trick, much like a custom tag does it for dispatching. Or maybe, and more likely, I'm too obtuse to see the real problem?  
  
> Another problem I see (maybe?) is with providing default implementations in concepts. Right now, we're able to introspect and check whether a type defines some operation. If not, then we can provide a default one. If we were to use ADL, we'd need to check whether the function call compiles using SFINAE, but whether the function call compiles depends on whether there's a default definition! We need something else to break this loop, and that's what hana::is_default and hana::default_ achieve.  
  
Actually one can provide default implementations for algorithms just fine, well, at least for the simpler case where only one default implementation is provided for a given algorithm, i.e., disregarding abstract concept classes like the ones Hana provides. As a matter of fact I've been experimenting with ADL as the customization point for [a heterogeneous library of my own](https://github.com/brunocodutra/alloy), and it seems to work like a charm for algorithms that operate on types the user controls. The problem really just arises for algorithms like `make_tuple` AFAICT.  
  
> I think I was also scared that overload resolution and ADL would be expensive, but I'm not sure whether that's a reasonable concern. I think it had some weight in the balance though.  
  
I was concerned about that at first too, but ADL itself turns out to be very cheap and most of the overhead actually comes from SFINAE friendliness. Surprisingly, after some playing with metabench one actually learns how to reduce the overhead, to the point it can be made just as fast as Hana. Hence my curiosity about the reasons behind your choice for tag dispatching.  
  
> Finally, I think the most important reason is that getting everything to work as it does (being able to provide default definitions, and even multiple sets of minimal complete definitions) was incredibly difficult. I remember it as being possibly the most frustrating and difficult part of all my work on Hana. It took weeks, if not months, and I tried so many things that never quite worked for subtle reasons. When I finally got it to work, I just went with it even though it was not perfect.  
   
Fair enough, I can see that it could get hairy to conciliate ADL with multiple sets of default implementations, depending on the abstract concept class.   
  
Thanks a lot for your thoughts on this matter!

---

## Comment 6

> Username: ldionne  
> Created at: 2017-02-25 03:06:51 UTC  
> Url: https://github.com/boostorg/hana/issues/321#issuecomment-282455323  

> But isn't the user usually interested in overriding algorithms precisely for types he introduces himself?  
  
Not always. For example, consider the external adapters for Fusion or `std::tuple`. I can't add something in an associated namespace of those types.
