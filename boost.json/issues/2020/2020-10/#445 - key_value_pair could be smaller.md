# #445 - key_value_pair could be smaller [Closed]

> Username: vinniefalco  
> Created at: 2020-10-08 19:58:26 UTC  
> Updated at: 2021-03-04 05:31:47 UTC  
> Closed at: 2021-03-04 05:31:47 UTC  
> Url: https://github.com/boostorg/json/issues/445  

By storing `len_` and `next_` in the allocated string itself, we can shrink `sizeof(key_value_pair)`. The benefits need to be measured.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-04 05:31:47 UTC  
> Url: https://github.com/boostorg/json/issues/445#issuecomment-790304941  

Hmm not such a great idea
