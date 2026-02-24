# #761 Fix is_floating_point symbol is not a class template [Merged]

> Username: sdebionne  
> Created at: 2025-04-04 14:35:36 UTC  
> Updated at: 2025-04-22 06:59:42 UTC  
> Merged at: 2025-04-04 16:27:30 UTC  
> Closed at: 2025-04-04 16:27:30 UTC  
> Url: https://github.com/boostorg/gil/pull/761  

Closes https://github.com/boostorg/gil/issues/760  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2025-04-04 16:26:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/761#pullrequestreview-2743572003  

Thank you!

---

## Comment 2

> Username: striezel  
> Created_at: 2025-04-04 17:42:45 UTC  
> Url: https://github.com/boostorg/gil/pull/761#issuecomment-2779394720  

Are you sure this is the correct fix? Some of the GCC and Clang builds now fail with errors like  
  
```  
./boost/gil/io/typedefs.hpp:35:51: error: template argument 1 is invalid  
   35 | template<> struct is_floating_point<gil::float64_t> : std::true_type {};  
      |                                                   ^  
```  
or  
```  
./boost/gil/io/typedefs.hpp:34:37: error: use of undeclared identifier 'gil'; did you mean 'boost::gil'?  
template<> struct is_floating_point<gil::float32_t> : std::true_type {};  
                                    ^~~  
                                    boost::gil  
```

---

## Comment 3

> Username: sdebionne  
> Created_at: 2025-04-04 18:56:48 UTC  
> Url: https://github.com/boostorg/gil/pull/761#issuecomment-2779523992  

This fix might have uncovered other issues 😅 . I'll have a look !

---

## Comment 4

> Username: sdebionne  
> Created_at: 2025-04-04 20:15:57 UTC  
> Url: https://github.com/boostorg/gil/pull/761#issuecomment-2779654089  

TBH I did not know that there was an implementation of `is_floating_point` in Boost.GIL. The file `io/typedefs.h` include <type_traits> though.

---

## Comment 5

> Username: striezel  
> Created_at: 2025-04-06 01:12:28 UTC  
> Url: https://github.com/boostorg/gil/pull/761#issuecomment-2781161234  

The only place where `is_floating_point` is used within GIL seems to be the TIFF I/O extension, and there it's only an implementation detail: https://github.com/boostorg/gil/blob/688acf78f7fc4ecb5b1c6f1e4a8f4a5939d28e9b/include/boost/gil/extension/io/tiff/detail/is_allowed.hpp#L101C13-L101C30  
  
Furthermore I'm a bit worried, because [cppreference.com states](https://en.cppreference.com/w/cpp/types/is_floating_point):  
  
> If the program adds specializations for `std::is_floating_point` or `std::is_floating_point_v`, the behavior is undefined.  
  
Doesn't sound like this is something one should do. As long as those lines were in the `boost` namespace (before the PR), that was not a specialization of `std::is_floating_point` but `boost::is_floating_point`, a different thing - although probably with similar intentions. But with the move to the `std` namespace it now is a specialization.

---

## Comment 6

> Username: sdebionne  
> Created_at: 2025-04-06 10:09:03 UTC  
> Url: https://github.com/boostorg/gil/pull/761#issuecomment-2781344419  

Ok let's revert my fix then and reopen the MR.

---
