# #24 Add move-construction and emplacement support [Closed]

> Username: Hincoin  
> Created at: 2016-04-11 23:20:45 UTC  
> Updated at: 2017-12-29 04:51:49 UTC  
> Closed at: 2017-12-29 04:51:49 UTC  
> Url: https://github.com/boostorg/lockfree/pull/24  

It is in many cases more efficient to move construct or `emplace` an item using constructor arguments than it is to copy construct. As of now, the only way to `push` onto `spsc_queue` is via copy construction with placement new. Adding support for emplacement and move construction and detecting these features' compatibility with compilers can allow for newer users to take advantage of them while still maintaining support for users with older compilers.

---

## Comment 1

> Username: glenfe  
> Created_at: 2016-07-14 15:22:50 UTC  
> Url: https://github.com/boostorg/lockfree/pull/24#discussion_r70825711  

You probably want to detect the absence of  
`BOOST_NO_CXX11_RVALUE_REFERENCES` for push, and the absence of both `BOOST_NO_CXX11_RVALUE_REFERENCES` and `BOOST_NO_CXX11_VARIADIC_TEMPLATES` for emplace.

---

## Comment 2

> Username: glenfe  
> Created_at: 2016-07-14 15:24:16 UTC  
> Url: https://github.com/boostorg/lockfree/pull/24#discussion_r70825959  

Perhaps `new (buffer + write_index) T{std::forward<Args>(args)...};`

---

## Comment 3

> Username: timblechmann  
> Created_at: 2017-12-29 04:51:49 UTC  
> Url: https://github.com/boostorg/lockfree/pull/24#issuecomment-354397722  

let's focus on #31

---
