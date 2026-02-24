# #293 - Tables should be aligned to element type [Closed]

> Username: sdkrystian  
> Created at: 2020-09-06 21:20:37 UTC  
> Updated at: 2020-10-16 20:31:06 UTC  
> Closed at: 2020-10-16 20:31:06 UTC  
> Url: https://github.com/boostorg/json/issues/293  

e.g. `object_impl::table` should be aligned to `alignof(key_value_pair)`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-06 21:25:47 UTC  
> Url: https://github.com/boostorg/json/issues/293#issuecomment-687905897  

need a static assert that the integer array which follows the key value pairs is also aligned

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-26 02:24:08 UTC  
> Updated at: 2020-09-26 02:27:03 UTC  
> Url: https://github.com/boostorg/json/issues/293#issuecomment-699277410  

`array` is correct

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-10-16 20:28:55 UTC  
> Url: https://github.com/boostorg/json/issues/293#issuecomment-710571933  

`object` is correct now

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-10-16 20:31:06 UTC  
> Url: https://github.com/boostorg/json/issues/293#issuecomment-710575538  

That's the last of it, strings don't need alignment.
