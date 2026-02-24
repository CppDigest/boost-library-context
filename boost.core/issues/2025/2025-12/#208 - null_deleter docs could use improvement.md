# #208 - null_deleter docs could use improvement [Closed]

> Username: vinniefalco  
> Created at: 2025-12-08 00:57:00 UTC  
> Updated at: 2025-12-08 09:21:22 UTC  
> Closed at: 2025-12-08 09:21:22 UTC  
> Url: https://github.com/boostorg/core/issues/208  

The null_deleter docs could also explain that it it useful when you don't have a dynamically allocated object at all. For example  
```  
struct impl { ... };  
  
shared_ptr<impl> get_default_impl()  
{  
  static impl def;  
  return shared_ptr<impl>( &def, null_deleter );  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-12-08 00:57:16 UTC  
> Url: https://github.com/boostorg/core/issues/208#issuecomment-3623995982  

@claude create a pull request to address this

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-12-08 01:37:12 UTC  
> Url: https://github.com/boostorg/core/issues/208#issuecomment-3624130117  

I'm going to try to fix this myself with AI help

---

## Comment 3

> Username: Lastique  
> Created at: 2025-12-08 09:21:22 UTC  
> Url: https://github.com/boostorg/core/issues/208#issuecomment-3625875152  

There is literally an example with `std::cout`. I don't see what else could be added that is currently missing.  
  
Also, as a general comment, no AI content in the docs please, as it raises authorship and licensing issues.
