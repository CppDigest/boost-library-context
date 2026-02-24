# #745 Portable minmax Win32-API workaround #744 [Merged]

> Username: ckormanyos  
> Created at: 2024-04-18 02:33:36 UTC  
> Updated at: 2024-04-18 08:21:09 UTC  
> Merged at: 2024-04-18 08:21:09 UTC  
> Closed at: 2024-04-18 08:21:09 UTC  
> Url: https://github.com/boostorg/gil/pull/745  

### Description  
  
The purpose of this PR is to work around min/max macro conflicts with windows.h when using Win32-API programming on MSVC (in Windows). Unfortunately windows.h defines min() and max() macros that conflict with the standard C++ ones.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-04-18 02:38:20 UTC  
> Url: https://github.com/boostorg/gil/pull/745#issuecomment-2062889326  

- The selected solution method uses parentheses like `(std::min)()`.  
- _Do_ _Not_ in this PR resort to `#define NOMINMAX`.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2024-04-18 03:48:40 UTC  
> Url: https://github.com/boostorg/gil/pull/745#issuecomment-2062939026  

Hi @mloskot this one cycled green and I think the trivial changes are ready for rewiew.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2024-04-18 08:20:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/745#pullrequestreview-2008180058  

LGTM. Thank you @ckormanyos

---
