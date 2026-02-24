# #114 - [review] remove the sequence algorithm metafunctions [Closed]

> Username: badair  
> Created at: 2017-04-09 03:38:41 UTC  
> Updated at: 2017-04-22 16:58:58 UTC  
> Closed at: 2017-04-22 16:58:58 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114  

from http://lists.boost.org/Archives/boost/2017/04/234190.php

---

## Comment 1

> Username: badair  
> Created at: 2017-04-09 03:45:25 UTC  
> Updated at: 2017-04-09 03:46:11 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-292761859  

suggestions from http://lists.boost.org/Archives/boost/2017/04/234218.php  
  
> Perhaps  
provide two 'get' functions and two 'replace' functions, one that includes  
the class in the tuple and one that doesn't.  
  
> The library could also define a special "varargs" type and include that in  
the tuple as the last argument when the function is C-variadic.

---

## Comment 2

> Username: badair  
> Created at: 2017-04-09 04:13:02 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-292762842  

consider keeping `pop_args_front` and `push_args_front`, as these are useful for "thunking" member funtions

---

## Comment 3

> Username: badair  
> Created at: 2017-04-14 18:54:38 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294213943  

closed via dc8df75e4f5f9ed31d863fc66e22195e9307e401

---

## Comment 4

> Username: ldionne  
> Created at: 2017-04-17 02:27:53 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294392166  

Why are `args_at`, `clear_args`, `pop_front_args`, `push_front_args`, `expand_args_right`, and `expand_args_left` still there? From the comments of the formal review, I am left under the impression that they are just as much out of scope as the other ones (it is also my personal impression), no?

---

## Comment 5

> Username: ldionne  
> Created at: 2017-04-17 02:41:51 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294393662  

Please reopen until this is completely solved.

---

## Comment 6

> Username: badair  
> Created at: 2017-04-18 01:43:22 UTC  
> Updated at: 2017-04-18 01:45:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294648040  

Guilty as charged. So, here is how I feel about each of these:  
  
1. I think `arg_at` is useful, even thought it is equivalent to `std::tuple_elment_t<N, args_t<T>>`. For instance, I recently had a real-world template interface that behaved differently for different `std::chrono::duration` types in a callback's parameter list. If the second parameter was a `std::chrono::seconds` type, then I did `foo()`. If it was a `std::chrono::microseconds` type, then I did `bar()`. I think `arg_at` is one of the more generically useful features in the library, despite its simplicity. I'm not super attached to it, but I would miss it in my own programming.  
  
2. I think `pop_front_args` and `push_front_args` work well for smoothing out the edges around member functions (`this` pointer etc). I found them useful in Eraserface [here](https://github.com/badair/eraserface/blob/master/include/eraserface/eraserface.hpp) for this purpose, which convinced me to keep them at the time. I'm not super attached to them, but they do provide a way out of needing to pull in an entire separate metaprogramming library for these little situations.  
  
3. `clear_args` and `expand_args*` are not very useful. These will be removed.  
  
Edit: formatting, grammar

---

## Comment 7

> Username: badair  
> Created at: 2017-04-18 01:51:01 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294649109  

@ldionne what do you think? Are my arguments for `arg_at` and `pop/push` persuasive, or do you still think they should be removed?

---

## Comment 8

> Username: ldionne  
> Created at: 2017-04-18 04:13:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-294670773  

More than anything, I would like to see what @brunocodutra and @pdimov think. My hunch would be that the case of `args_at` is more easily justifiable than that of `pop_front_args` and `push_front_args`.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-04-18 22:30:07 UTC  
> Updated at: 2017-04-18 22:30:45 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-295002835  

What I think is that the typical workflow when manipulating the parameter list would be  
  
1. extract the parameters into a tuple/`metal::list`  
2. manipulate the tuple/list using mp11/metal/hana/whatever  
3. replace the original parameter list with the transformed one  
  
For step 1, I think that in addition to the current extractor, it would be useful to also have one that ignores the class, so that `R (T::*)(A1, A2)` gives `tuple<A1, A2>`.  
  
For step 3, there's no direct way at present, if I'm not mistaken.  
  
So for instance, to take at random the first code example from Barrett's link, `forward_all` would be something like  
  
```  
template<class F> using forward_all = replace_args_t<F, mp_transform<forward_t, extract_args_t<F>>>;  
```  
  
where I'm using the arbitrary names `replace_args_t` and `extract_args_t` to signify steps 3 and 1 from the above.  
  
Note that, if we assume that `extract_args_t` doesn't include the class, this `forward_all` works for any functions or member functions, preserving the return type, the class, the `noexcept` clause, the qualifiers, if any. The original `forward_all` would transform `int (X::*)(int) noexcept` to `int (int const&)`.

---

## Comment 10

> Username: pdimov  
> Created at: 2017-04-18 22:36:37 UTC  
> Updated at: 2017-04-18 22:39:11 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-295004193  

Reading a bit further from the Eraserface link, there's `expand_args_t` called on `remove_member_pointer<F>`, which is another point in favor of having this built-in.  
  
Varargs complicate matters a bit. We want `replace<extract>` to be identity, so there are two options: one, `extract` gives us a last element of a tag type denoting the ellipsis, or two, it doesn't give us that, but `replace` preserves it.

---

## Comment 11

> Username: pdimov  
> Created at: 2017-04-18 22:56:43 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-295008892  

Reading furtherer,  
  
https://github.com/badair/eraserface/blob/master/include/eraserface/eraserface.hpp#L295  
  
`pop_front_args_t` is used on `function_type_t<Pmf>` to remove the class argument. But if we have an extractor that ignores it, we would no longer need to `pop_front` it.  
  
Then `expand_args_right_t` is used without any left args, which is the same as `expand_args_t`.  
  
I'm not sure how you'd use `push_front` to add a class parameter, because while there's a way to go from member to free (`function_type_t`), there's no corresponding way to go from free to member (right?)  
  
You can go from an argument list to a member pointer with `apply_member_pointer`, but there you supply the class directly so there's no need to `push_front` it.  
  
So in summary I think that the main use of `pop_front` is to pop the class which is only needed because of the common case of extracting the arguments without the class. In fact this could even be made the only accessor as the current `args` would be `extract<function_type_t<F>>`.

---

## Comment 12

> Username: brunocodutra  
> Created at: 2017-04-19 20:30:48 UTC  
> Updated at: 2017-04-19 20:31:25 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-295427784  

Somehow I missed this discussion.  
  
I guess I'm with @pdimov here, I think pop/push metafunctions are too generic in this context. Assuming the most common use-cases are really to just push/pop the class type out of/into arguments lists, it might not be immediately obvious to the user that `pop_front_args/push_front_args` are meant for this purpose, so I think it would be better to provide specific traits to cover these use-cases.  
  
Regarding `arg_at` I concur that it's probably the most useful meta-algorithm currently provided, but since it's available in the standard library for `std::tuple`s, which are used as type lists by default, I don't really see a reason to duplicate it in the library. The time invested in testing/documenting/improving/maintaining it in the future would be much better spent on the core traits.

---

## Comment 13

> Username: badair  
> Created at: 2017-04-19 23:38:31 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-295494529  

@pdimov @brunocodutra thanks for weighing in. I'm convinced that I should remove these. I'll also think about the best way to represent the `extract`/`replace` semantics.

---

## Comment 14

> Username: badair  
> Created at: 2017-04-22 16:58:58 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/114#issuecomment-296386851  

Fixed via 78e4b1f21213167c51c2d2f4c33e4a0726d4bed2
