# #116 changes in Boost.Optional expected failures [Closed]

> Username: akrzemi1  
> Created at: 2016-02-19 14:07:33 UTC  
> Updated at: 2017-10-14 15:48:48 UTC  
> Closed at: 2017-10-14 15:37:29 UTC  
> Url: https://github.com/boostorg/boost/pull/116  



---

## Comment 1

> Username: grafikrobot  
> Created_at: 2016-02-19 14:57:53 UTC  
> Url: https://github.com/boostorg/boost/pull/116#discussion_r53469963  

It might be better to only evaluate the test in the platforms matter in the build file. That way there are less testing resources used.

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2016-02-19 15:12:54 UTC  
> Url: https://github.com/boostorg/boost/pull/116#discussion_r53471915  

I would be happy to do it, but can you give me any hint on how I can configure this in the build file?

---

## Comment 3

> Username: danieljames  
> Created_at: 2017-06-23 20:25:36 UTC  
> Url: https://github.com/boostorg/boost/pull/116#issuecomment-310764669  

This doesn't validate. I don't think `test` is allowed in `mark-unusable` - as it marks *every* test as unusable.  
  
    explicit-failures-markup.xml:3095: element test: Schemas validity error : Element 'test': This element is not expected. Expected is ( toolset ).  
    explicit-failures-markup.xml fails to validate

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-10-14 15:37:29 UTC  
> Url: https://github.com/boostorg/boost/pull/116#issuecomment-336642959  

This isn't relevant any longer, is it? As the markup is now local.

---

## Comment 5

> Username: danieljames  
> Created_at: 2017-10-14 15:48:48 UTC  
> Url: https://github.com/boostorg/boost/pull/116#issuecomment-336643779  

It can still be updated here if that's wanted, anything that's not in a module is left alone.

---
