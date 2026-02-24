# #220 [experimental] printable optional [Merged]

> Username: ricejasonf  
> Created at: 2015-12-10 03:59:36 UTC  
> Updated at: 2015-12-11 17:37:35 UTC  
> Merged at: 2015-12-11 17:03:54 UTC  
> Closed at: 2015-12-11 17:03:54 UTC  
> Url: https://github.com/boostorg/hana/pull/220  

"Just" for kicks :stuck_out_tongue:

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2015-12-10 05:36:36 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163492346  

crap.. Sorry I didn't name the branch appropriately. I must also confess that it is only by the luck that the header files are alphabetized. :sweat_smile:

---

## Comment 2

> Username: ldionne  
> Created_at: 2015-12-10 16:39:49 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47250201  

This will make a copy of `x` because you take by value. Can you please use `auto const& x`?

---

## Comment 3

> Username: ldionne  
> Created_at: 2015-12-10 16:41:05 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47250391  

When the optional is empty, you return `"nothing"`. Hence the return type will be deduced to be `char*` (or `char const*`, not sure), which is wrong. To make sure that we're returning a `std::string`, you could specify the return type as `std::string`. Or perhaps that was done on purpose?

---

## Comment 4

> Username: ldionne  
> Created_at: 2015-12-10 16:42:51 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47250636  

Instead of `"just x"`, I think it would be preferable to have `just(x)`. This way, if `x` is something complex, it might be easier to tell when the string representation of `x` finishes (because of the closing parenthesis). What do you think?

---

## Comment 5

> Username: ldionne  
> Created_at: 2015-12-10 16:43:40 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47250753  

Technically, this is not required because you don't use `std::string` directly.

---

## Comment 6

> Username: ldionne  
> Created_at: 2015-12-10 16:43:53 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47250782  

You probably want your name here instead of mine!

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2015-12-10 17:23:51 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47255958  

Perhaps they should all specify `std::string` as a return type, especially print_t::(). (I see it is specified in the doxygen version).

---

## Comment 8

> Username: ricejasonf  
> Created_at: 2015-12-10 17:34:06 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47257158  

Do you prefer I use `->` notation for the return type?

---

## Comment 9

> Username: ricejasonf  
> Created_at: 2015-12-10 17:43:49 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47258315  

I was on the fence about it. I almost did `Just 5` to be like haskell, though I am not a haskell programmer.

---

## Comment 10

> Username: ricejasonf  
> Created_at: 2015-12-10 18:12:20 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163706273  

I realized that I should probably also add an include to `fwd/optional.hpp` in `printable.hpp`.  
  
I took the liberty of specifying the return type for `print_t::operator()` to `std::string` which requires the removal of `constexpr`, so I removed `constexpr` in the doxygen part as well. Let me know if that is a mistake.  
  
I cleaned up the other stuff you mentioned and used `->` when specifying a return type (lmk if you don't like that). Should I create a new pull request with a single commit and a better branch name?   
`experimental/printable_optional` ??

---

## Comment 11

> Username: ldionne  
> Created_at: 2015-12-10 18:13:54 UTC  
> Updated_at: 2015-12-10 23:22:05 UTC  
> Url: https://github.com/boostorg/hana/pull/220#discussion_r47262186  

Hmm, you're right; `hana::print` documents that it returns `std::string`, yet `print_impl::apply` does not enforce it. I guess the right thing to do is to specify the return type of all the `print_impl::apply`s to be `std::string`. Can you change that while you're at it? Also, I'd use normal return type syntax, since it's more consistent with the rest of the code. I rarely use trailing return type syntax elsewhere in the code (it just happened to be so).

---

## Comment 12

> Username: ldionne  
> Created_at: 2015-12-10 18:20:58 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163708328  

> I realized that I should probably also add an include to fwd/optional.hpp in printable.hpp.  
  
That's right, good catch.  
  
> I took the liberty of specifying the return type for print_t::operator() to std::string which requires the removal of constexpr, so I removed constexpr in the doxygen part as well. Let me know if that is a mistake.  
  
This is fine. I'm not sure about removing the `constexpr` keyword though, since it really says that the function object itself is constexpr, only its `operator()` isn't anymore. So technically, it's correct to leave `constexpr`.  
  
Still, I think we should do as you suggest and remove it, since it will convey the proper intuition for most users. I think most users wouldn't see the situation the same way I do, and they would find it confusing.  
  
> I cleaned up the other stuff you mentioned and used -> when specifying a return type (lmk if you don't like that).   
  
Good, but please use regular return type syntax. I'm not strongly attached to either (and in general I would probably pick the trailing syntax), but it's less consistent with the rest of the code.  
  
> Should I create a new pull request with a single commit and a better branch name? experimental/printable_optional ??  
  
No, just update your commit locally and `push --force` to the `printable_optional` branch of your own fork. That'll update this pull request.

---

## Comment 13

> Username: ricejasonf  
> Created_at: 2015-12-10 18:31:54 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163710914  

Oh, now that you explain it... The function object itself IS constexpr. I was looking at the pseudo-lambda next the struct function object, which caused my confusion.

---

## Comment 14

> Username: ldionne  
> Created_at: 2015-12-10 19:23:19 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163724780  

Looks good to me. When the CI is green, can you please squash these two commits together, write a simple but meaningful commit message for the added feature and then `push --force` to update this pull request with the single, squashed commit. I'll then merge.

---

## Comment 15

> Username: ricejasonf  
> Created_at: 2015-12-10 19:55:53 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163732991  

I made a third commit changing the return type for all `print_impl`s. I only saw you mention that in a github email. I still don't see it in the converstation.

---

## Comment 16

> Username: ldionne  
> Created_at: 2015-12-10 20:07:43 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163735896  

See [above](https://github.com/boostorg/hana/pull/220#discussion_r47262186). It's a comment on an _outdated diff_, as GitHub says. I can't say I'm a fan of GitHub's code review facilities, really.

---

## Comment 17

> Username: ldionne  
> Created_at: 2015-12-11 17:04:31 UTC  
> Url: https://github.com/boostorg/hana/pull/220#issuecomment-163991515  

Thank you!

---
