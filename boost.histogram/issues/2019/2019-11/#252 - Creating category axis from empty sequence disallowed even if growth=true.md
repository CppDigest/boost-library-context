# #252 - Creating category axis from empty sequence disallowed even if growth=true [Closed]

> Username: henryiii  
> Created at: 2019-11-18 18:18:12 UTC  
> Updated at: 2019-11-18 22:21:53 UTC  
> Closed at: 2019-11-18 22:21:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/252  

Currently, the check for bins does not take into account growth; this makes it impossible to create an empty category axis, for example. The check maybe should look like this instead:  
  
```cpp  
if (size() == 0 && !options_type::test(option::growth))  
    BOOST_THROW_EXCEPTION(std::invalid_argument("bins > 0 required unless growth is enabled"));  
```  
  
(Verified to work in a simple case)

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-11-18 20:39:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/252#issuecomment-555198879  

It is possible to create an empty category axis, just use the default ctor, see axis_category_test.cpp:121.  
  
Nevertheless, passing an empty sequence should be allowed, too, when growth is on, good catch.

---

## Comment 2

> Username: henryiii  
> Created at: 2019-11-18 20:58:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/252#issuecomment-555205860  

It's not possible to make an empty category axis with metadata, then, unless the metadata is set later.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-11-18 21:50:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/252#issuecomment-555225449  

See https://github.com/HDembinski/histogram/pull/81

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-11-18 22:21:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/252#issuecomment-555236653  

fixed in develop
