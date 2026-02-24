# #284 Use detail::decay instead of std::decay in implementation [Closed]

> Username: vittorioromeo  
> Created at: 2016-05-30 15:19:15 UTC  
> Updated at: 2016-06-13 00:28:32 UTC  
> Closed at: 2016-06-13 00:28:32 UTC  
> Url: https://github.com/boostorg/hana/pull/284  

- In implementation code, occurrences of `typename std::decay<...>::type` have been replaced with `typename hana::detail::decay<...>::type`.  
- The rationale for this change, as mentioned in #282, is that `hana::detail::decay` performs better than `std::decay` in "libc++". I also think that using the same `decay` implementation throughout the library is more consistent.

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-05-31 01:46:54 UTC  
> Updated_at: 2016-05-31 14:56:31 UTC  
> Url: https://github.com/boostorg/hana/pull/284#discussion_r65115020  

Can you please sort the `#includes` alphabetically? This is really a small detail, but I try to keep them sorted as it makes it easier to detect duplicates and similar errors.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-05-31 01:47:27 UTC  
> Updated_at: 2016-05-31 14:56:31 UTC  
> Url: https://github.com/boostorg/hana/pull/284#discussion_r65115036  

Same here, please sort.

---

## Comment 3

> Username: vittorioromeo  
> Created_at: 2016-05-31 07:47:25 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-222615858  

Done (assuming you still want `/fwd/...` includes to appear first.)

---

## Comment 4

> Username: ldionne  
> Created_at: 2016-05-31 14:40:59 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-222709721  

Yeah, that's perfect. Thanks a lot!

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-05-31 14:42:57 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-222710353  

Ah, I stumbled on another thing. It sometimes is possible to remove `<type_traits>` includes since nothing in it is used anymore. That's the case at least for `comparing.hpp`. Can you have a look? I know these are all details, but I think that keeping a code base clean is a matter of these.

---

## Comment 6

> Username: vittorioromeo  
> Created_at: 2016-05-31 15:03:45 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-222717126  

I've removed unnecessary `#include <type_traits>` directives in the files where I replaced `std::decay` with `detail::decay`.

---

## Comment 7

> Username: ldionne  
> Created_at: 2016-06-11 21:39:38 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-225395981  

Please rebase on top of `develop` and this should pass the CI (I fixed the Travis build). Sorry for the annoyance.

---

## Comment 8

> Username: ldionne  
> Created_at: 2016-06-11 21:40:36 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-225396034  

Oh, and while you're at it, it would be great if you could squash these three commits into one, since that's really just one logical change set.

---

## Comment 9

> Username: ldionne  
> Created_at: 2016-06-12 19:58:25 UTC  
> Url: https://github.com/boostorg/hana/pull/284#issuecomment-225456989  

Nevermind, I merged manually. Thanks a lot for the patch!

---
