# #17 - Add support for class template [Closed]

> Username: sdebionne  
> Created at: 2021-06-15 08:39:55 UTC  
> Updated at: 2022-02-13 01:46:40 UTC  
> Closed at: 2022-02-13 01:46:40 UTC  
> Url: https://github.com/boostorg/describe/issues/17  

Would support for class template description be implementable (eventually with a different macro)?  
  
```cpp  
namespace app {  
    template <typename T>  
    struct point  
    {  
        T x;  
        T y;  
    };  
  
    BOOST_DESCRIBE_STRUCT(point, (), (x, y))      // Not supported  
    BOOST_DESCRIBE_STRUCT(point<int>, (), (x, y)) // OK  
} //namespace app  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2021-06-15 14:37:50 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861555782  

Class templates already work with BOOST_DESCRIBE_CLASS. (But MSVC can't compile it due to compiler bugs.)

---

## Comment 2

> Username: sdebionne  
> Created at: 2021-06-15 19:32:27 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861775591  

Awesome, I was unlucky then. Just to be sure, `BOOST_DESCRIBE_CLASS` works with class templates but not `BOOST_DESCRIBE_STRUCT` or am I reading too much between the lines? It's just that `BOOST_DESCRIBE_CLASS` is slightly more intrusive, being defined inside the class...

---

## Comment 3

> Username: pdimov  
> Created at: 2021-06-15 19:39:53 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861780000  

That's correct, BOOST_DESCRIBE_CLASS works, BOOST_DESCRIBE_STRUCT doesn't (work for templates).  
  
However I notice I don't actually have a test for it. I probably intended to add one and got distracted with other things.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-06-15 19:43:08 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861781930  

Oh it works with VS 16.10. Cool.

---

## Comment 5

> Username: sdebionne  
> Created at: 2021-06-15 20:03:03 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861794067  

Thanks for clarifying. Making `BOOST_DESCRIBE_STRUCT` work for templates, would that be possible? If not, I'll just close the issue.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-06-15 22:19:04 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861873006  

No, it can't work as is. It might be possible to have another macro `BOOST_DESCRIBE_TEMPLATE_STRUCT` but the syntax in the general case will be awful, I think.

---

## Comment 7

> Username: pdimov  
> Created at: 2021-06-16 01:48:14 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861946651  

It turns out it only works with VS2019 with /std:c++latest. :-)

---

## Comment 8

> Username: pdimov  
> Created at: 2021-06-16 01:58:07 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-861958399  

https://github.com/boostorg/describe/commit/7e9f1f0eb9759a0b75f0ba8097764c9146c0cf49

---

## Comment 9

> Username: sdebionne  
> Created at: 2021-10-19 14:13:09 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-946766920  

> It might be possible to have another macro BOOST_DESCRIBE_TEMPLATE_STRUCT but the syntax in the general case will be awful, I think.  
  
Any chance that you got new ideas how to implement a non-intrusive description of class template ? Just checking before giving it a try...

---

## Comment 10

> Username: pdimov  
> Created at: 2021-10-19 14:25:16 UTC  
> Url: https://github.com/boostorg/describe/issues/17#issuecomment-946779098  

No, I haven't been considering it.
