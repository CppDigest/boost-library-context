# #145 - Addressing the limitation of the LOOPHOLE [Open]

> Username: schaumb  
> Created at: 2023-11-23 17:38:11 UTC  
> Updated at: 2024-02-17 09:29:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/145  

There is currently a limitation in the code for LOOPHOLE:  
  
> None of the member fields should have a template constructor with one parameter.  
  
This limitation could be solved using the following strategy: [link](https://godbolt.org/z/nrjx19Mce)

---

## Comment 1

> Username: schaumb  
> Created at: 2023-11-29 12:56:14 UTC  
> Url: https://github.com/boostorg/pfr/issues/145#issuecomment-1831846883  

(this works only when the template constructor is SFINAE friendly)

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-02-17 09:29:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/145#issuecomment-1949914735  

Could you please provide a PR to the library?
