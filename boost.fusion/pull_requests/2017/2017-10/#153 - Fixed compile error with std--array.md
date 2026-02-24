# #153 Fixed compile error with std::array [Merged]

> Username: Flast  
> Created at: 2017-10-13 11:07:25 UTC  
> Updated at: 2017-10-13 13:12:06 UTC  
> Merged at: 2017-10-13 13:12:02 UTC  
> Closed at: 2017-10-13 13:12:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/153  

libc++ enables std::array in c++98 mode, but <type_traits> doesn't.

---

## Comment 1

> Username: Flast  
> Created_at: 2017-10-13 13:09:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/153#issuecomment-336447904  

Yeah! Travis now shows sane result!

---
