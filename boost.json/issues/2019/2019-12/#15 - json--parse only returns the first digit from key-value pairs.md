# #15 - json::parse only returns the first digit from key-value pairs [Closed]

> Username: PkmX  
> Created at: 2019-12-02 18:51:55 UTC  
> Updated at: 2019-12-03 04:00:36 UTC  
> Closed at: 2019-12-03 04:00:36 UTC  
> Url: https://github.com/boostorg/json/issues/15  

The assertion  
  
```cpp  
assert(json::parse("{\"port\": 12345}").as_object().at("port").as_int64() == 12345);  
```  
  
fails as only 1 is returned.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-03 03:15:04 UTC  
> Url: https://github.com/boostorg/json/issues/15#issuecomment-560979513  

That's pretty lame, lol... how did this get past testing???

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-12-03 03:22:12 UTC  
> Url: https://github.com/boostorg/json/issues/15#issuecomment-560980903  

I can reproduce it, thanks for a clear test case - investigating.
