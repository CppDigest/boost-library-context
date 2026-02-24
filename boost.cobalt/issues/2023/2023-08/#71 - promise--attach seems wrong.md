# #71 - promise::attach seems wrong [Closed]

> Username: anarthal  
> Created at: 2023-08-14 08:29:19 UTC  
> Updated at: 2023-08-31 03:37:36 UTC  
> Closed at: 2023-08-31 03:37:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/71  

```  
    void detach() {attached_ = false;}  
    void attach() {attached_ = false;}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-14 09:08:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/71#issuecomment-1676963057  

That doesn't only seem wrong.
