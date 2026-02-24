# #63 - Use boost.mp11 instead of boost.mpl [Closed]

> Username: HDembinski  
> Created at: 2018-12-05 08:28:25 UTC  
> Updated at: 2018-12-05 16:43:09 UTC  
> Closed at: 2018-12-05 08:32:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/63  

Since the library is C++14, it should use boost.mp11 rather than boost.mpl. boost.mp11 is faster and allows one to write cleaner code that's easier to maintain.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-12-05 08:32:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/63#issuecomment-444401490  

Nevermind, my comment was based on reading the docs. Then I looked into the source, seems like safe_numerics does not use boost.mpl directly. I suppose you listed it as a requirement because the boost libraries you depend on depend themselves on the MPL.

---

## Comment 2

> Username: robertramey  
> Created at: 2018-12-05 16:43:08 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/63#issuecomment-444554659  

I've recently removed the last dependence on MPL and also on Boost Preprocessor.  In it's place I'm using mp11.  I will upload changes soon.
