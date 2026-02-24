# #32 - Is the boost::optional interface backwards compatible? [Closed]

> Username: mg262  
> Created at: 2017-05-22 15:36:03 UTC  
> Updated at: 2017-11-06 07:04:08 UTC  
> Closed at: 2017-11-06 07:04:08 UTC  
> Url: https://github.com/boostorg/optional/issues/32  

I am creating a library that returns `boost::optional<string>`s.  If I develop against `boost::optional` 1.62, and users try to use a later version of `boost::optional`, what will happen?

---

## Comment 1

> Username: fcacciola  
> Created at: 2017-05-22 16:57:52 UTC  
> Url: https://github.com/boostorg/optional/issues/32#issuecomment-303158902  

Future versions of boost::optional will be backward-compatible.  
However, we now have std::optional, and is available in some newest compilers such as Visual Studio 2017. so, if you are just starting to use this, it might be better to adopt the std version if you can.

---

## Comment 2

> Username: mg262  
> Created at: 2017-11-04 11:04:43 UTC  
> Url: https://github.com/boostorg/optional/issues/32#issuecomment-341888476  

Further to this -- we've built a library against boost::optional 1.62, but some of the targets that need to be built against the library may have trouble using that recent a v. of boost.  Was there any point in the _past_ development of boost when there would have been an incompatibility? E.g. if users use boost::optional 1.0.0, would that cause an issue?  
  (Thanks!)

---

## Comment 3

> Username: akrzemi1  
> Created at: 2017-11-04 17:08:59 UTC  
> Url: https://github.com/boostorg/optional/issues/32#issuecomment-341913397  

There were slight changes that may render some previous code invalid. For instance, in pre-C++11 versions conversion to bool was not explicit, and the following was possible:  
```c++  
optional<T> o;  
bool b = o;  
```   
This will now not compile, and we consider it a positive breaking change.   
  
Similarly, the addition of move semantics to `boost::optional` caused some rare code to fail to compile on older MSVC versions that implemented move semantics incorrectly.  
  
Also, the implementation of optional references worked incorrectly on some compilers due to compiler bugs. Now in order to avoid bugs, on this compilers run-time bugs have been turned into compile-time failures.  
  
But in general our goal is not to change the interface. Even deprecated functions are not removed.  Any regressions if present are unintentional.

---

## Comment 4

> Username: mg262  
> Created at: 2017-11-06 07:04:06 UTC  
> Url: https://github.com/boostorg/optional/issues/32#issuecomment-342063059  

Thank you very much for this detailed reply.
