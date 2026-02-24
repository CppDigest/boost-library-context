# #75 - Integrate toolbox extension into GIL core [Open]

> Username: stefanseefeld  
> Created at: 2018-04-07 20:07:25 UTC  
> Updated at: 2018-10-23 17:10:50 UTC  
> Url: https://github.com/boostorg/gil/issues/75  

Some of the IO support code in `boost/gil/io/` depends on the toolbox extension (i.e., includes `boost/gil/extension/toolbox/*.hpp`).  
Break out the relevant bits from the toolbox extension and integrate them into the GIL core library itself.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-04-08 16:39:59 UTC  
> Url: https://github.com/boostorg/gil/issues/75#issuecomment-379563805  

It is a good idea.

---

## Comment 2

> Username: mloskot  
> Created at: 2018-06-25 16:45:19 UTC  
> Url: https://github.com/boostorg/gil/issues/75#issuecomment-400018546  

@stefanseefeld Since,   
- is not assigned to [The Grand Merge](https://github.com/boostorg/gil/milestone/1)  
- no work has been done for this so far, AFAIK  
I'm removing it from Boost 1.68 and postponing.  
  
Feel free to update if you think otherwise.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-10-05 20:43:38 UTC  
> Url: https://github.com/boostorg/gil/issues/75#issuecomment-427493272  

While cleaning the headers lately, I've added some `TODO` comments about dependency on the toolbox and dynamic_image extensions.
