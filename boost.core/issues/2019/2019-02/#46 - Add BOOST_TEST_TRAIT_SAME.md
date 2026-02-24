# #46 - Add BOOST_TEST_TRAIT_SAME [Closed]

> Username: HDembinski  
> Created at: 2019-02-05 21:17:39 UTC  
> Updated at: 2019-02-21 09:10:57 UTC  
> Closed at: 2019-02-21 09:10:55 UTC  
> Url: https://github.com/boostorg/core/issues/46  

When testing results of TMP, I very very often do  
```  
BOOST_TEST_TRAIT_TRUE((std::is_same<Foo, Bar>));  
```  
It would be nice to have a shortcut macro for this common case.  
```  
BOOST_TEST_TRAIT_SAME(Foo, Bar);  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2019-02-05 21:20:41 UTC  
> Updated at: 2019-02-05 21:21:11 UTC  
> Url: https://github.com/boostorg/core/issues/46#issuecomment-460808182  

The problem with that is when `Foo` or `Bar` have a comma inside. `__VA_ARGS__` but that doesn't always work. Worth thinking about.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-02-06 16:15:07 UTC  
> Url: https://github.com/boostorg/core/issues/46#issuecomment-461083262  

Turns out that `__VA_ARGS__` is supported since msvc-8.0, so it could be relied upon nowadays. Added `BOOST_TEST_TRAIT_SAME` on develop.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-02-07 09:55:45 UTC  
> Url: https://github.com/boostorg/core/issues/46#issuecomment-461353883  

Cool, great!

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-02-21 09:10:55 UTC  
> Url: https://github.com/boostorg/core/issues/46#issuecomment-465920505  

It seems to work fine on develop, so I am closing this.
