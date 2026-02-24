# #180 Make fallback `is_complete` implementation more robust [Merged]

> Username: Lastique  
> Created at: 2023-05-21 12:12:14 UTC  
> Updated at: 2025-04-21 11:36:43 UTC  
> Merged at: 2025-04-21 11:16:06 UTC  
> Closed at: 2025-04-21 11:16:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180  

When all else fails, at least indicate the types that we always know to be incomplete: cv `void` and boundless arrays.  
  
Also cleanup formatting a bit and added tests.

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-05-23 08:37:22 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-1558810628  

The CI failures are not related to this PR. The CI config needs to be updated to remove the use of deprecated GHA features.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-04-15 15:07:24 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-2805953477  

Ping?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-04-15 17:58:10 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-2807040128  

Apologies, I'm trying to get CI working again, but see https://github.com/boostorg/boostdep/issues/28

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2025-04-21 08:18:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-2817916218  

@Lastique can you merge latest develop into this so the CI can run now?  Thanks!

---

## Comment 5

> Username: Lastique  
> Created_at: 2025-04-21 09:51:26 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-2818067615  

Done, CI has passed.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2025-04-21 11:16:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/180#issuecomment-2818205972  

Thanks!

---
