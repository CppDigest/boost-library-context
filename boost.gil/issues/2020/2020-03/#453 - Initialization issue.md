# #453 - [dynamic] Initialization issue [Closed]

> Username: sdebionne  
> Created at: 2020-03-16 10:38:18 UTC  
> Updated at: 2021-05-10 19:22:33 UTC  
> Closed at: 2020-04-24 10:24:59 UTC  
> Url: https://github.com/boostorg/gil/issues/453  

### Actual behavior  
  
```c++  
boost::gil::any_image<  
  boost::mp11::mp_list<  
    boost::gil::gray8_image_t  
  >  
> img = boost::gil::gray8_image_t();  
```  
  
Fails to compile with  
  
```  
error: conversion from 'boost::gil::gray8_image_t {aka boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t> > >, false, std::allocator<unsigned char> >}' to non-scalar type 'boost::gil::any_image<boost::mp11::mp_list<boost::gil::image<boost::gil::pixel<unsigned char, boost::gil::layout<boost::mp11::mp_list<boost::gil::gray_color_t>, boost::mp11::mp_list<std::integral_constant<int, 0> > > >, false, std::allocator<unsigned char> > > >' requested  
```  
  
while  
  
```c++  
boost::gil::any_image<  
  boost::mp11::mp_list<  
    boost::gil::gray8_image_t  
  >  
> img(boost::gil::gray8_image_t());  
```  
  
compiles fine.  
  
### Expected  behavior  
  
It would be nice if both initializations work the same.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/dynamic_image/any_image.hpp>  
namespace gil = boost::gil;  
int main  
{  
  any_image<boost::mp11::mp_list<gray8_image_t>> img = gray8_image_t();  
}  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: GCC 7.3  
- Build settings: -std=gnu++1z  
- Version (Git ref or `<boost/version.hpp>`): 1.75

---

## Comment 1

> Username: mloskot  
> Created at: 2020-03-18 20:57:20 UTC  
> Updated at: 2020-03-18 20:57:57 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-600854614  

@sdebionne yes, it is desired. I have only applied very minimal C++11 modernisation to the dynamic_image extension, it needs more.  
  
By the way, we are likely removing some of apparently dead code from the dynamic_image extension (see #281) and we will also be looking into adding move semantics to image classes (see #438).

---

## Comment 2

> Username: sdebionne  
> Created at: 2020-03-19 13:14:13 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-601170525  

@mloskot I can have a shot at #438 (I think this issue is related) in nobody is working on it.

---

## Comment 3

> Username: mloskot  
> Created at: 2021-05-06 21:59:55 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-833898207  

@sdebionne I discovered that we might have lost the `boost::gil::any_image<boost::mp11::mp_list<...>>` completely, what is somewhat related to this issue.  
  
There have been number of changes to `any_image` and `apply_operation` recently and earlier, and I might have got lost.  
Could you update me on what is currently supported form of specialising `any_image`?  
  
Here is what I'm testing using Boost 1.76, https://godbolt.org/z/4Ta31Tf3P, and below:  
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/dynamic_image/any_image.hpp>  
#include <boost/mp11.hpp>  
namespace gil = boost::gil;  
  
int main()  
{  
    // OK  
    {  
        gil::any_image  
        <  
            gil::gray8_image_t,  
            gil::rgb8_image_t  
        > image;  
        image.dimensions();  
    }  
    // not OK  
    {  
        gil::any_image  
        <  
            boost::mp11::mp_list  
            <  
                gil::gray8_image_t,  
                gil::rgb8_image_t  
            >  
        > image;  
        image.dimensions();  
    }  
}  
```

---

## Comment 4

> Username: sdebionne  
> Created at: 2021-05-07 05:54:31 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-834087773  

The first form is the only one supported for `any_image` and `any_image_view`. It's indeed a breaking change that was maybe not advertised enough. But I believe the [doc is up-to-date](https://www.boost.org/doc/libs/develop/libs/gil/doc/html/design/dynamic_image.html).  
I'm a bit surprised that I reported this issue with `boost::mp11::mp_list<`, that probably means it predates the final variant2 refactoring. The use of inheriting constructors b8f48bcae747530d7a803e4cbcc96613d84223a5 should fix #453.

---

## Comment 5

> Username: mloskot  
> Created at: 2021-05-08 20:57:14 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-835510519  

@sdebionne Thank for the update. Yes, it does predate the variant2.  
  
I will have to catch up the IO tests with those changes.  
I missed that because of the still convoluted way of running the IO tests, what tests are run by our CI, what `#define`-s are set, etc. so it needs clearing to try avoid any conditional running of tests.

---

## Comment 6

> Username: sdebionne  
> Created at: 2021-05-10 07:00:51 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-836266681  

I thought I had updated all the tests, but since I am usually not running the tests locally with IO tests, I might have left some behind... sorry about that. Indeed, it would be great to have the CI catch these oversights.

---

## Comment 7

> Username: mloskot  
> Created at: 2021-05-10 19:22:33 UTC  
> Url: https://github.com/boostorg/gil/issues/453#issuecomment-837189286  

@sdebionne No problem, not your fault, but the tests configuration - it should be no brainer w/o any manual tweaks required.
