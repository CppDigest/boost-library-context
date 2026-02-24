# #2233 - ref #2231 - use of posix SSIZE_MAX [Closed]

> Username: madmongo1  
> Created at: 2021-05-13 18:36:37 UTC  
> Updated at: 2021-06-06 17:51:18 UTC  
> Closed at: 2021-06-06 17:51:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2233  

see #2231  
  
POSIX defined SSIZE_MAX but there is no guarantee in C++ that this symbol will be defined (even on a posix build environment) by including `<limits>`.  
  
This is causing a compile error for one of our users.  
  
Poposed fix is to use `std::make_unsigned<std::size_t>` and the corresponding `std::numeric_limits<T>::max()` in place of `ssize_t` and `SSIZE_MAX`.

---

## Comment 1

> Username: viccpp  
> Created at: 2021-05-14 06:11:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2233#issuecomment-841033298  

> Poposed fix is to use `std::make_unsigned<std::size_t>`  
  
`make_signed`?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-05-14 06:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2233#issuecomment-841034344  

Yep :)
