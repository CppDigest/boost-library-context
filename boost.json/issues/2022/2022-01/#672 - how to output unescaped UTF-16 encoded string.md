# #672 - how to output unescaped UTF-16 encoded string [Closed]

> Username: missdeer  
> Created at: 2022-01-19 06:00:50 UTC  
> Updated at: 2022-01-19 07:37:55 UTC  
> Closed at: 2022-01-19 07:37:55 UTC  
> Url: https://github.com/boostorg/json/issues/672  

I'm using Boost 1.78.0, want to get serialize result like   
```js  
{  
    "key": "\u1234"  
}  
```  
Is it possible with current release?

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-01-19 06:16:43 UTC  
> Url: https://github.com/boostorg/json/issues/672#issuecomment-1016119836  

Your example doesn't explain much. You want to serialize json as a UTF-16 document? Or you have a string with text in UTF-16 and you want to re-encode it as UTF-8?

---

## Comment 2

> Username: missdeer  
> Created at: 2022-01-19 06:57:57 UTC  
> Url: https://github.com/boostorg/json/issues/672#issuecomment-1016139686  

```cpp  
boost::json::object rootObj;  
rootObj["key"]="一些用UTF-8编码的CJK字符"; // some CJK characters encoded as UTF-8  
  
std::string json = boost::json::serialize(rootObj);  
```  
I want to get `json` like something in my initial post.

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-01-19 07:07:35 UTC  
> Url: https://github.com/boostorg/json/issues/672#issuecomment-1016144984  

So, you want to output in UTF-16? We don't support serialization as UTF-16 (or UTF-32) at this point.

---

## Comment 4

> Username: missdeer  
> Created at: 2022-01-19 07:37:55 UTC  
> Url: https://github.com/boostorg/json/issues/672#issuecomment-1016162675  

Got it. Thanks.
