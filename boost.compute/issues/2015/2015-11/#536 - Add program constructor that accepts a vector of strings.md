# #536 - Add program constructor that accepts a vector of strings [Closed]

> Username: svenstaro  
> Created at: 2015-11-28 03:41:26 UTC  
> Updated at: 2015-12-26 23:15:14 UTC  
> Closed at: 2015-12-26 23:15:11 UTC  
> Url: https://github.com/boostorg/compute/issues/536  

The official OpenCL C++ bindings provide a program constructor that accepts a vector of strings for sources. Presumably this would be used to load a list of files into the vector. As it stands in boost compute, the way to obtain the same behavior is making a really long concatenated string and pass that instead.

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-12-01 18:10:00 UTC  
> Url: https://github.com/boostorg/compute/issues/536#issuecomment-161050894  

Yeah, that shouldn't be too hard. And like you said, the best way currently is to create a single concatenated string.

---

## Comment 2

> Username: kylelutz  
> Created at: 2015-12-04 08:32:23 UTC  
> Url: https://github.com/boostorg/compute/issues/536#issuecomment-161908096  

Implemented in PR #539.

---

## Comment 3

> Username: svenstaro  
> Created at: 2015-12-04 08:42:39 UTC  
> Url: https://github.com/boostorg/compute/issues/536#issuecomment-161909501  

Sweet!
