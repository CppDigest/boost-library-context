# #65 - "inline" linkage necessary [Open]

> Username: aaron-michaux  
> Created at: 2018-11-21 14:33:17 UTC  
> Updated at: 2023-11-27 14:50:58 UTC  
> Url: https://github.com/boostorg/optional/issues/65  

The following lines in `optional.hpp` should have "inline" linkage. This is necessary for using `<boost/optional/optional.hpp>` in a module.  
  
```  
    const in_place_init_t in_place_init((in_place_init_t::init_tag()));  
  
   const in_place_init_if_t  
       in_place_init_if((in_place_init_if_t::init_tag()));  
  
```

---

## Comment 1

> Username: aaron-michaux  
> Created at: 2018-11-21 14:34:01 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-440682624  

I'm not sure how this code could be correct in a header-only library without `inline`.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-11-21 14:54:28 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-440691581  

global objects declared `const` have internal linkage.

---

## Comment 3

> Username: aaron-michaux  
> Created at: 2018-11-21 17:12:01 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-440742906  

Okay. The code breaks with `-fmodules-ts`, but that could be a bug in the  
standard or WIP implementation too.  
  
Aaron Michaux  
PhD ECE, Purdue University  
(765)-444-9413  
  
  
  
  
On Wed, Nov 21, 2018 at 9:54 AM Andrzej Krzemieński <  
notifications@github.com> wrote:  
  
> global objects declared const have internal linkage.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/optional/issues/65#issuecomment-440691581>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AEYDyDEyKAbYs0icD6uImEUj2lSFs-K_ks5uxWklgaJpZM4YtPS0>  
> .  
>

---

## Comment 4

> Username: gast128  
> Created at: 2023-11-27 13:08:51 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-1827803580  

Can't these be turned into inline constexpr variables with BOOST_INLINE_CONSTEXPR. I had 2 hits:  
- const in_place_init_t in_place_init ((in_place_init_t::init_tag()));  
- const in_place_init_if_t in_place_init_if ((in_place_init_if_t::init_tag()));

---

## Comment 5

> Username: akrzemi1  
> Created at: 2023-11-27 14:26:33 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-1827939342  

> Can't these be turned into inline constexpr variables with BOOST_INLINE_CONSTEXPR.  
  
They could in C++17. But this has to work in C++11. I could special-case it for C++17, but there have to be sufficient motivation. Can someone point me to the place (in the IS perhaps) that tells if/why the current implementation is incompatible with modules?

---

## Comment 6

> Username: gast128  
> Created at: 2023-11-27 14:41:27 UTC  
> Updated at: 2023-11-27 14:50:58 UTC  
> Url: https://github.com/boostorg/optional/issues/65#issuecomment-1827967659  

> They could in C++17. But this has to work in C++11  
  
I think BOOST_INLINE_CONSTEXPR expands to 'inline constexpr' when available and otherwise to 'const'. It may miss the 'static const' case though. Perhaps there is a Boost guideline.
