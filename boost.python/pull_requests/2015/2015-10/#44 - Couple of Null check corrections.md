# #44 Couple of Null check corrections [Open]

> Username: ya1gaurav  
> Created at: 2015-10-14 10:30:22 UTC  
> Updated at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/44  

1. m_to_python is checked against Null at line 70, but not checked at 89.  
2. !! is same as without using this.

---

## Comment 1

> Username: ya1gaurav  
> Created_at: 2015-12-08 07:02:24 UTC  
> Url: https://github.com/boostorg/python/pull/44#issuecomment-162796009  

Please review updated patches. Thanks.

---

## Comment 2

> Username: hotgloupi  
> Created_at: 2015-12-08 09:16:03 UTC  
> Updated_at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/44#discussion_r46929881  

You are checking if `node` is not null two times instead of one. And the `delete` operator is a no-op when the pointer is null, this whole thing can be simply written as:  
  
```  
if (node != 0)  
    delete_node(node->next);  
delete node;  
```

---

## Comment 3

> Username: ya1gaurav  
> Created_at: 2015-12-09 03:26:48 UTC  
> Updated_at: 2020-11-14 02:30:49 UTC  
> Url: https://github.com/boostorg/python/pull/44#discussion_r47047532  

I agree delete NULL is safe.

---
