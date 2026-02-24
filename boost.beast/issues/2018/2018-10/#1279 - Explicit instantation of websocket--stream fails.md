# #1279 - Explicit instantation of websocket::stream fails [Closed]

> Username: djarek  
> Created at: 2018-10-29 17:44:00 UTC  
> Updated at: 2019-02-03 14:51:40 UTC  
> Closed at: 2018-11-27 16:35:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1279  

https://wandbox.org/permlink/NhmB6f9y3wanpVdz

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-04 02:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-435636542  

Well, this is kind of tricky, because `pmd_opts_` is not present if `deflateSupported==false`. But with explicit instantiation, the compiler has to emit both functions just in case. One solution is to just put `pmd_opts_` in the declaration even if it isn't needed. I don't know what else to do. @glenfe ?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-11-04 02:22:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-435636581  

Some light reading:  
https://stackoverflow.com/questions/40183520/explicit-template-instantiation-and-sfinae

---

## Comment 3

> Username: djarek  
> Created at: 2018-11-05 06:59:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-435773055  

@vinniefalco I believe that if `open_pmd` used SFINAE, this problem would go away, because the compiler wouldn't try to instantiate both.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-11-06 23:54:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-436452563  

it has to instantiate both, how does it know ahead of time which one it will need?

---

## Comment 5

> Username: djarek  
> Created at: 2018-11-07 16:00:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-436674731  

https://godbolt.org/z/QltKnm

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-03 14:51:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1279#issuecomment-460058364  

This will help people reduce their build times by putting the definitions in a .cpp file instead of the .hpp.
