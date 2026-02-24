# #58 - Comparing strings does not compile in gcc 7+ in C++17 mode [Closed]

> Username: akrzemi1  
> Created at: 2017-10-16 12:49:00 UTC  
> Updated at: 2017-10-20 06:47:18 UTC  
> Closed at: 2017-10-19 19:53:48 UTC  
> Url: https://github.com/boostorg/container/issues/58  

The following program does not compile in GCC 7, in C++17 mode:  
  
```c++  
#include <boost/container/string.hpp>  
  
int main()  
{  
    boost::container::string s1, s2;  
    return s1 == s2;  
}  
```  
  
This might be a bug in GCC (it works in clang), but I am not sure. It boils to the following situation, which does not compile in GCC 7 (C++17):  
  
```c++  
template <class T, class U = int>  
    class C  
    {};  
  
template <class T, class U>  
    void f(const C<T, U>&) {}  
  
template <class T, template <class> class CT>  
    void f(CT<T>) {}  
  
int main()  
{  
    C<int> s;  
    f(s);  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-10-16 16:44:28 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-336947507  

This looks like a compiler bug, because an exact match is availablein the overload set, so I don't get why is ambiguous. Also, in C++14 mode code compiles fine.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2017-10-16 16:51:12 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-336949852  

I'll try to commit soon a patch to SFINAE out the string view overload, which is not a bad idea in general to avoid ambiguities.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2017-10-16 19:45:02 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-337013964  

I got the following background information on isocpp.org: https://groups.google.com/a/isocpp.org/d/topic/std-discussion/B1MHPRMqfFA/discussion

---

## Comment 4

> Username: igaztanaga  
> Created at: 2017-10-16 20:13:09 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-337024059  

Thanks, for the DR link. So it's time to patch the implementation...

---

## Comment 5

> Username: akrzemi1  
> Created at: 2017-10-19 07:36:19 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-337824643  

One more link that might be useful: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=82579  
GCC developers say that the new rules for template-tempate argument matching have a regression in the Standard that they are forced to implement the regression too, but there is a flag in GCC to disable the feature: `-fno-new-ttp-matching`.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2017-10-19 19:54:36 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-338018628  

I tested locally with Mingw-w64 GCC 7.1. Let me know if there are remaining issues. Thanks for all!

---

## Comment 7

> Username: akrzemi1  
> Created at: 2017-10-20 06:47:18 UTC  
> Url: https://github.com/boostorg/container/issues/58#issuecomment-338120651  

I only found it in Wandbox. I will not be able to test it until it is released. The source looks fine to me.
