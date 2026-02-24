# #277 - Is there something like std::is_callable in Hana? [Closed]

> Username: viboes  
> Created at: 2016-05-22 21:34:34 UTC  
> Updated at: 2016-05-24 21:50:19 UTC  
> Closed at: 2016-05-24 20:54:47 UTC  
> Url: https://github.com/boostorg/hana/issues/277  



---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-05-22 22:21:24 UTC  
> Updated at: 2016-05-22 22:21:34 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-220860157  

I don't think so. There is a collection of hana wrapped traits in traits.hpp, but `std::is_callable` is not in there. Wrapping it in `hana::trait` is pretty simple though. The function will return an `IntegralConstant`.  
  
``` cpp  
constexpr auto is_callable = hana::trait<std::is_callable>{};  
  
BOOST_HANA_CONSTANT_CHECK(is_callable(hana::type_c<blah>));  
```

---

## Comment 2

> Username: ricejasonf  
> Created at: 2016-05-22 22:30:20 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-220860577  

There is also this, but I have not had a chance to use it much.  
  
https://github.com/badair/callable_traits

---

## Comment 3

> Username: pfultz2  
> Created at: 2016-05-23 01:54:48 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-220872248  

The Fit library does provide [`is_callable`](http://fit.readthedocs.io/en/latest/is_callable/index.html), and it works for any [`Callable`](http://fit.readthedocs.io/en/latest/concepts/index.html#callable).

---

## Comment 4

> Username: viboes  
> Created at: 2016-05-23 05:04:09 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-220888050  

@ricejasonf Yes, but `std::is_callable` is C++1z ;-)  
@ricejasonf The `is_callable` in CallableTraits doesn't use `std::invoke`/`INVOKE`  
@pfultz2 What about adapting it to the C++1z style?  
@pfultz2 We need `std::invoke` in Boost :)

---

## Comment 5

> Username: pfultz2  
> Created at: 2016-05-23 12:35:11 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-220967712  

> @pfultz2 What about adapting it to the C++1z style?  
  
What is C++1z style?  
  
> @pfultz2 We need std::invoke in Boost :)  
  
There is [`boost::hana::apply`](http://boostorg.github.io/hana/group__group-functional.html#ga30027c383676084be151ef3c6cf2829f).

---

## Comment 6

> Username: badair  
> Created at: 2016-05-23 15:54:28 UTC  
> Updated at: 2016-05-23 16:22:46 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221016554  

> The is_callable in CallableTraits doesn't use std::invoke/INVOKE  
  
@viboes The `is_callable` in CallableTraits was a misnomer, and has been removed entirely on the develop branch. The [`can_invoke`](http://badair.github.io/callable_traits/doc/html/callable_traits/ref_can_invoke.html) function on the master branch is logically equivalent to `std::is_callable`, and is defined in terms of `std::invoke` (reference documentation linked). On the develop branch, it has been renamed to `is_invokable`. It works for many compilers, including recent versions of MSVC.  
  
I guess it needs to be removed from my library entirely, since (apparently) Fit has this covered already. I'm in the process of removing pieces of the library and dumping them into standalone headers, so I'll do the same with this. CallableTraits will possibly be renamed to MemberFunctionTraits in the end. I'm trying to eliminate scope creep.  
  
Either way, my library is undergoing many changes, so you probably don't want to use mine right now. If you want, I can let you know if/when I break out this functionality into a standalone header, although it may be a couple of weeks or so before I can get around to it.  
  
> We need std::invoke in Boost :)  
  
Agreed.  
  
@pfultz2 What is the compiler support for Fit's `is_callable`? If you want, I could see about merging our implementations to include support for Visual Studio (if it isn't already supported).  
  
Edit: I edited this many times, sorry if you were caught reading between edits

---

## Comment 7

> Username: pfultz2  
> Created at: 2016-05-23 16:50:04 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221029001  

> @pfultz2 What is the compiler support for Fit's is_callable? If you want, I could see about merging our implementations to include support for Visual Studio (if it isn't already supported).  
  
Its supported on MSVC 2015. The Fit library doesn't support an earlier version of MSVC, so I've never tested it.

---

## Comment 8

> Username: viboes  
> Created at: 2016-05-23 19:00:34 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221063524  

@pfultz2 http://en.cppreference.com/w/cpp/types/is_callable  
  
boost::hana::apply. don't takes invoke in account :(

---

## Comment 9

> Username: pfultz2  
> Created at: 2016-05-23 19:23:53 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221069467  

> @pfultz2 http://en.cppreference.com/w/cpp/types/is_callable  
  
I see, well the `is_callable<F(Ts...)>` style will never happen. It is problematic and should never be used.  
  
> boost::hana::apply. don't takes invoke in account :(  
  
What do you mean? The docs say it is equivalent.

---

## Comment 10

> Username: viboes  
> Created at: 2016-05-23 21:00:26 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221094776  

@pfultz2 Right. I was confused by  
  
```  
= [](auto&& f, auto&& ...x) -> decltype(auto) {  
        return forwarded(f)(forwarded(x)...);  
    }  
```  
  
How `is_callable<F(Ts...)>` style  it is problematic?

---

## Comment 11

> Username: pfultz2  
> Created at: 2016-05-23 21:24:25 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221100818  

> How is_callable<F(Ts...)> style it is problematic?  
  
It decays arrays to pointers and discards top-level cv-qualifiers. This can produce incorrect results.

---

## Comment 12

> Username: viboes  
> Created at: 2016-05-24 06:01:22 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221174265  

I don't see how this problems disappear with `is_callable<Fn, Args...>` style?

---

## Comment 13

> Username: pfultz2  
> Created at: 2016-05-24 06:10:16 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221175564  

> I don't see how this problems disappear with is_callable<Fn, Args...> style?  
  
The problem doesn't exist. For example, `is_callable<F, int[3]>` will check if `F` is callable with `int[3]`, but `is_callable<F(int[3])>` will check if `F` is callable with `int*`, which is different.

---

## Comment 14

> Username: viboes  
> Created at: 2016-05-24 17:32:07 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221345833  

I don't reach to see why `is_callable<F(int[3])>` will behave differently than `is_callable<F, int[3]>`. Are the definitions different? What are the definitions of both functions that make this different? Sorry, I'm missing something obvious to you.

---

## Comment 15

> Username: pfultz2  
> Created at: 2016-05-24 20:37:07 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221393728  

> I don't reach to see why is_callable<F(int[3])> will behave differently than is_callable<F, int[3]>.  
  
They are different because of the way C++ works. `is_callable<F(int[3])>` is the same as calling `is_callable<F(int*)>`, whereas `is_callable<F, int[3]>` and `is_callable<F, int*>` are different. You can try it for yourself with the fit library:  
  
``` cpp  
#include <fit.hpp>  
  
struct F  
{  
    bool operator()(int (&&arr)[3]) const;  
};  
  
struct G  
{  
    bool operator()(int*) const;  
};  
  
template<class Sig>  
struct is_callable_sig;  
  
template<class F, class... Ts>  
struct is_callable_sig<F(Ts...)>  
: fit::is_callable<F, Ts...>  
{};  
  
int main() {  
    static_assert(fit::is_callable<F, int[3]>(), "Callable");  
    static_assert(is_callable_sig<F(int[3])>(), "Not Callable with function signature");  
  
    static_assert(fit::is_callable<G, int*>(), "Callable");  
    static_assert(is_callable_sig<G(int*)>(), "Callable");  
}  
```  
  
The above will break with `static_assert` failure saying "Not Callable with function signature".

---

## Comment 16

> Username: ldionne  
> Created at: 2016-05-24 20:54:47 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221398159  

As others pointed out, Hana does not provide `std::is_callable` and I would say it is out of scope for Hana to provide this. Actually, the whole `Functional` part should really be part of a separate library (it's not used inside Hana except for examples) and it will eventually be deprecated in favour of Fit.

---

## Comment 17

> Username: viboes  
> Created at: 2016-05-24 21:30:58 UTC  
> Updated at: 2016-05-24 21:35:02 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221407590  

@pfultz2  I'm not sure this is an issue or it was intentional. I believe that you should report it on std-proposal ML.  
  
http://melpon.org/wandbox/permlink/VxEgirJh4f1gPzSi

---

## Comment 18

> Username: pfultz2  
> Created at: 2016-05-24 21:50:19 UTC  
> Url: https://github.com/boostorg/hana/issues/277#issuecomment-221412287  

> I'm not sure this is an issue or it was intentional.  
  
It is an issue, whether the committee thinks its important is another matter.
