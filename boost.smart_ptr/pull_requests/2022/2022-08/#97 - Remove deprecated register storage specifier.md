# #97 Remove deprecated register storage specifier [Closed]

> Username: georgthegreat  
> Created at: 2022-08-06 21:27:55 UTC  
> Updated at: 2022-10-19 20:21:02 UTC  
> Closed at: 2022-10-19 18:44:26 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/97  

Deprecated as of C++11 and removed in C++17.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-08-08 07:43:16 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/97#issuecomment-1207779349  

@jwillikers Does this look good to you?

---

## Comment 2

> Username: jwillikers  
> Created_at: 2022-10-19 16:23:43 UTC  
> Updated_at: 2022-10-19 16:24:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/97#issuecomment-1284272434  

@pdimov I should be able to have this change tested on our PPC hardware.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-10-19 16:31:13 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/97#issuecomment-1284281063  

Please test #100 instead, which removes `register` from more places. Thanks in advance.

---

## Comment 4

> Username: SiliconA-Z  
> Created_at: 2022-10-19 20:21:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/97#issuecomment-1284528813  

Not so fast, this happened when I removed the register keyword from the parameter, but here it works fine.

---
