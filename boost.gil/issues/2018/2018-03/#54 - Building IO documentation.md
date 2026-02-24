# #54 - Building IO documentation [Closed]

> Username: chhenning  
> Created at: 2018-03-20 14:31:40 UTC  
> Updated at: 2018-04-02 18:14:57 UTC  
> Closed at: 2018-04-02 18:14:57 UTC  
> Url: https://github.com/boostorg/gil/issues/54  

We do have the quickbook documentation here:  
  
https://github.com/boostorg/gil/tree/develop/io/doc  
  
Does someone know how to build this into html?

---

## Comment 1

> Username: mloskot  
> Created at: 2018-03-20 14:37:04 UTC  
> Url: https://github.com/boostorg/gil/issues/54#issuecomment-374621000  

There is `Jamfile`, so isn't enough to run?  
```  
cd  io/doc  
b2  
```  
  
I don't know what `standalone` means. Boost.Geometry does not use it, https://github.com/boostorg/geometry/blob/develop/doc/Jamfile.v2#L32

---

## Comment 2

> Username: mloskot  
> Created at: 2018-03-20 14:39:58 UTC  
> Url: https://github.com/boostorg/gil/issues/54#issuecomment-374622065  

By the way, the docs is a whole separate area that deserves some love, converting to .qbk (or markdown, whatever is accepted by Boost) would improve the library experience a lot :)  
If there is plan, before or after the grand merge, we could discuss it

---

## Comment 3

> Username: chhenning  
> Created at: 2018-03-20 14:51:56 UTC  
> Url: https://github.com/boostorg/gil/issues/54#issuecomment-374626632  

Yes, you're correct. Docs are important and need a lot of attention to detail. For the time being I suggest we combine gil's html with the already built io's html.

---

## Comment 4

> Username: mloskot  
> Created at: 2018-03-20 15:00:54 UTC  
> Url: https://github.com/boostorg/gil/issues/54#issuecomment-374630192  

OK, I'll follow your and @stefanseefeld's lead. There is already a lot of tasks to complete.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-04-02 18:14:57 UTC  
> Url: https://github.com/boostorg/gil/issues/54#issuecomment-377999003  

The entire documentation build process has been reworked in https://github.com/boostorg/gil/commit/e38fe881c82644f162bbf0d6fdae5b4a7fb1c854.
