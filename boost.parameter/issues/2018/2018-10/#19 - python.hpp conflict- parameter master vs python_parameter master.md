# #19 - python.hpp conflict: parameter master vs python_parameter master [Closed]

> Username: mloskot  
> Created at: 2018-10-25 10:54:08 UTC  
> Updated at: 2018-10-29 01:42:41 UTC  
> Closed at: 2018-10-29 01:42:41 UTC  
> Url: https://github.com/boostorg/parameter/issues/19  

TBH, I am not clear where this bug report belongs to: boostorg/boost, boostorg/parameter or boostorg/parameter_python. Trying here.  
  
On Linux, I've got fresh super-project at `master`, bootstrapped, while deploying the headers I'm getting  
  
```  
$ b2 headers  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
error: Cannot create link boost/parameter/python.hpp to libs/parameter/include/boost/parameter/python.hpp.  
error: Link previously defined to another file, libs/parameter_python/include/boost/parameter/python.hpp.```  
```  
  
That is because the Boost super-project points to the two master which both include the `python.hpp`.  
  
`https://github.com/boostorg/parameter_python/tree/master/include/boost/parameter/python.hpp`  
conflicts with  
`https://github.com/boostorg/parameter/blob/master/include/boost/parameter/python.hpp`

---

## Comment 1

> Username: mloskot  
> Created at: 2018-10-25 12:30:21 UTC  
> Url: https://github.com/boostorg/parameter/issues/19#issuecomment-433031693  

/cc to @pdimov as per "I'll need to take a look at what's on master" on the Slack

---

## Comment 2

> Username: eldiener  
> Created at: 2018-10-25 16:57:52 UTC  
> Url: https://github.com/boostorg/parameter/issues/19#issuecomment-433128030  

I was going to fix this but Peter has already done so. Please pull parameter again and retry b2 headers.

---

## Comment 3

> Username: mloskot  
> Created at: 2018-10-25 18:56:01 UTC  
> Url: https://github.com/boostorg/parameter/issues/19#issuecomment-433166376  

@eldiener Yeah, I've managed to work around it. Thanks
