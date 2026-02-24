# #258 - *** argument error in /home/kyb/boost-src/tools/build/src/tools/common.jam:977: in toolset-tag [Closed]

> Username: kuvaldini  
> Created at: 2019-03-06 15:42:23 UTC  
> Updated at: 2020-06-10 13:24:50 UTC  
> Closed at: 2020-06-10 13:24:50 UTC  
> Url: https://github.com/boostorg/boost/issues/258  

```  
$ ./b2 install  
/home/kyb/boost-src/tools/build/src/tools/common.jam:977: in toolset-tag  
*** argument error  
* rule numbers.less ( n1 n2 )  
* called with: ( 4 )  
* missing argument n2  
```

---

## Comment 1

> Username: kuvaldini  
> Created at: 2019-03-06 15:46:11 UTC  
> Url: https://github.com/boostorg/boost/issues/258#issuecomment-470158084  

I "fixed" this by commenting part of line  
  
      if $(tag) = gcc #&& [ numbers.less 4 $(version[1]) ]

---

## Comment 2

> Username: arre525  
> Created at: 2019-03-26 07:58:55 UTC  
> Url: https://github.com/boostorg/boost/issues/258#issuecomment-476513949  

Thanks. Had the same problem when trying to crosscompile boost 1.70 for arm.

---

## Comment 3

> Username: dec1  
> Created at: 2019-04-22 15:41:20 UTC  
> Url: https://github.com/boostorg/boost/issues/258#issuecomment-485453556  

see also https://github.com/boostorg/build/issues/385

---

## Comment 4

> Username: jdmairs  
> Created at: 2020-01-02 23:18:37 UTC  
> Url: https://github.com/boostorg/boost/issues/258#issuecomment-570394811  

Same problem was in 1.71 building with Petalinux ARM gcc toolkit.  
So thankful for your fix!
