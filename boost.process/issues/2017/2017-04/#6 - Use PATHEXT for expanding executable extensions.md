# #6 - Use PATHEXT for expanding executable extensions [Closed]

> Username: pravic  
> Created at: 2017-04-22 17:18:54 UTC  
> Updated at: 2017-04-22 17:37:27 UTC  
> Closed at: 2017-04-22 17:37:27 UTC  
> Url: https://github.com/boostorg/process/issues/6  

Currently it uses hardcoded limited [set of them](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/search_path.hpp#L35): `"", ".exe", ".com", ".bat"`, but it is not correct.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2017-04-22 17:36:54 UTC  
> Url: https://github.com/boostorg/process/issues/6#issuecomment-296389216  

Oh ok, what's the error?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2017-04-22 17:37:27 UTC  
> Url: https://github.com/boostorg/process/issues/6#issuecomment-296389257  

closed in favor of the other repository.
