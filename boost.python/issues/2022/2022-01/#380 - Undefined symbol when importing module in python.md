# #380 - Undefined symbol when importing module in python [Open]

> Username: gordon7155  
> Created at: 2022-01-10 12:49:10 UTC  
> Updated at: 2022-05-18 09:48:06 UTC  
> Url: https://github.com/boostorg/python/issues/380  

Hi guys  
  
I''m trying to use boost python and then I'm exposing simple class i get   
undefined symbol: _ZN5boost6python15instance_holder8allocateEP7_objectmm  
  
I checked symbols in libboost_python39.so.1.78.0 and i noticed that exist following symbol  
_ZN5boost6python15instance_holder8allocateEP7_objectmmm  (one m more than expected)  
  
So am I doing sth wrong or this is a bug?  
  
My class  
```  
struct ConnectionFactory  
{     
    ConnectionFactory(){}   
    ~ConnectionFactory(){}  
};  
```  
  
Exposing by:  
`class_<ConnectionFactory>("ConnectionFactory");`  
  
Linux Ubuntu 18.04  
  
EDIT:  
I made a workaround, just modifying expected symbol using this   
`objcopy --redefine-sym _ZN5boost6python15instance_holder8allocateEP7_objectmm=_ZN5boost6python15instance_holder8allocateEP7_objectmmm myFile.o`   
  
And this is working, but i'm not sure if this is correct approach

---

## Comment 1

> Username: joriscarrier  
> Created at: 2022-05-18 09:48:06 UTC  
> Url: https://github.com/boostorg/python/issues/380#issuecomment-1129802747  

>   
  
hi,  
  
i've the same problem with boost 1.78.0 but not with boost.1.76.0
