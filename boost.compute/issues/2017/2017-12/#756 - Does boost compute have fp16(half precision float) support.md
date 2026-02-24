# #756 - Does boost compute have fp16(half precision float) support ? [Open]

> Username: 9prady9  
> Created at: 2017-12-12 17:40:37 UTC  
> Updated at: 2019-03-12 23:17:43 UTC  
> Url: https://github.com/boostorg/compute/issues/756  



---

## Comment 1

> Username: WilliamTambellini  
> Created at: 2017-12-12 18:16:31 UTC  
> Url: https://github.com/boostorg/compute/issues/756#issuecomment-351138727  

Good question.  
I dont see any fp16 type in:  
https://github.com/boostorg/compute/blob/master/include/boost/compute/types/fundamental.hpp

---

## Comment 2

> Username: velhaco20000  
> Created at: 2019-03-12 00:04:47 UTC  
> Url: https://github.com/boostorg/compute/issues/756#issuecomment-471790285  

Someone is implementing or you need help ?

---

## Comment 3

> Username: velhaco20000  
> Created at: 2019-03-12 00:06:34 UTC  
> Url: https://github.com/boostorg/compute/issues/756#issuecomment-471790653  

The problem with this implementation is the cl_khr_fp16 extension.  
This implies a mechanism to enable extensions automatically.

---

## Comment 4

> Username: velhaco20000  
> Created at: 2019-03-12 02:40:23 UTC  
> Updated at: 2019-03-12 02:50:36 UTC  
> Url: https://github.com/boostorg/compute/issues/756#issuecomment-471828641  

My first atempt is :  
 -create an extension dir structure;  
-create the pragma for each extension;  
-create a mechanism to activate extensions at program beggining;  
-inject the pragmas when compiles;  
-eliminate errors resulting from pragmas;  
-test only with fp16 for now

---

## Comment 5

> Username: velhaco20000  
> Created at: 2019-03-12 23:17:43 UTC  
> Url: https://github.com/boostorg/compute/issues/756#issuecomment-472217396  

Update : some extensions were upgraded to core implementation. This means the extension mechanism is not so simple, instead , it will control OpenCL API version.  
I will read the code to understand how it implements the API.
