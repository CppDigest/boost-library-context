# #15 fix transform_params/ fix integer_sequence for clang [Merged]

> Username: jamboree  
> Created at: 2014-04-25 05:13:35 UTC  
> Updated at: 2014-07-20 22:06:56 UTC  
> Merged at: 2014-04-25 06:01:40 UTC  
> Closed at: 2014-04-25 06:01:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/15  



---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-04-26 23:52:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/15#discussion_r12026960  

Is there a reason for `unrefcv` instead of `std::decay`? Most of the time when qualifications are being removed, `decay` is intended instead.

---

## Comment 2

> Username: jamboree  
> Created_at: 2014-04-27 03:32:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/15#discussion_r12027460  

I have to keep array, or it will decay to T*.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-04-27 19:29:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/15#discussion_r12030796  

In that case I would suggest not mixing aliases and classes, for consistency. In this case, I would rather have a regular template class named `remove_cv_ref` or similar, and we can follow the `_t` suffix convention for defining aliases for type traits if the need arises later.  
  
I believe the need for arrays to not be decayed will be explained in my other comments about an array wrapper. Otherwise, please would you tell me in what concrete scenario this would be needed?

---

## Comment 4

> Username: jamboree  
> Created_at: 2014-04-28 01:35:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/15#discussion_r12033004  

`unrefcv` is nothing more than a shortcut, so it should be as short as possible.  
And given the fact that std::make_integer_sequence is also an alias without _t suffix, I don't think it'd be a universal convention that we should always follow.

---

## Comment 5

> Username: K-ballo  
> Created_at: 2014-04-28 03:08:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/15#discussion_r12033986  

`make_integer_sequence` is not a type trait, but part of the integer sequence utilities. When dealing with type traits, I don't want to have to remember if something is an alias or a class (and thus whether do I have to use typename with it, can I use it with lazy evaluation, do I need `mpl::identity<...>` and so on).

---
