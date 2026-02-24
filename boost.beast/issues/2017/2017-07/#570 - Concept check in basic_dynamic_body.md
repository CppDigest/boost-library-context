# #570 - Concept check in basic_dynamic_body [Closed]

> Username: vinniefalco  
> Created at: 2017-07-02 00:16:50 UTC  
> Updated at: 2017-07-12 14:16:09 UTC  
> Closed at: 2017-07-12 14:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/570  

```  
static_cast(is_dynamic_buffer<DynamicBuffer>::value, "");  
```

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-07-02 10:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/570#issuecomment-312484229  

Do you mean `static_cast` or `static_assert`?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-02 12:23:45 UTC  
> Url: https://github.com/boostorg/beast/issues/570#issuecomment-312488805  

`static_assert` lol

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-12 14:16:09 UTC  
> Url: https://github.com/boostorg/beast/issues/570#issuecomment-314782820  

This is done
