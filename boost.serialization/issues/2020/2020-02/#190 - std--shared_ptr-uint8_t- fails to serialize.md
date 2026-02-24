# #190 - std::shared_ptr<uint8_t[]> fails to serialize [Closed]

> Username: knzivid  
> Created at: 2020-02-20 17:12:14 UTC  
> Updated at: 2020-02-21 07:17:13 UTC  
> Closed at: 2020-02-21 07:17:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/190  

```  
struct Test {  
    std::shared_ptr<uint8_t[]> ptr;  
  
    template <typename Archive>  
    void serialize(Archive &ar, size_t) {  
        ar & ptr;  
    }  
};  
```  
  
https://www.godbolt.org/z/X3eLnS  
  
This is with boost 1.71  
  
Am I missing something here?

---

## Comment 1

> Username: robertramey  
> Created at: 2020-02-21 05:31:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/190#issuecomment-589505336  

yep you won't be able to  serialization a declaration.  You need to serialize some real data.  So  
  
uint8_t x[10];  
std::shared_ptr<uint8_t> px = x;   
..  
  
ar << x will work.
