# #421 - Review layout of space-critical classes [Closed]

> Username: vinniefalco  
> Created at: 2017-06-04 16:53:37 UTC  
> Updated at: 2017-09-01 03:15:07 UTC  
> Closed at: 2017-09-01 03:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/421  

Critical classes like `header`, `message`, `basic_fields` should have their members reviewed to determine if a rearrangement of order results in a space savings.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-04 17:03:17 UTC  
> Url: https://github.com/boostorg/beast/issues/421#issuecomment-306052752  

See https://wandbox.org/permlink/7jGi94XsRIxgbCmq

---

## Comment 2

> Username: glenfe  
> Created at: 2017-06-04 20:38:31 UTC  
> Url: https://github.com/boostorg/beast/issues/421#issuecomment-306065364  

Make the following change to Empty in the unit test empty_base_optimzation_test.cpp and observe which C++ implementations fail to compile it, in various C++ standard modes:  
  
```cpp  
struct Empty final {  
    operator bool() { return true; }  
};  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-09-01 03:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/421#issuecomment-326476812  

Done
