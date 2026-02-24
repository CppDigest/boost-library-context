# #87 - continuation [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-08-22 03:21:59 UTC  
> Updated at: 2023-09-04 06:55:06 UTC  
> Closed at: 2023-09-04 06:55:05 UTC  
> Url: https://github.com/boostorg/cobalt/issues/87  

A possible option is to allow a simple co_composed op like this:  
  
```cpp  
void co_composed(async::completion_handler<int, double> ch)  
{  
   ...   
   co_return {42, 2.4};  
}  
```  
  
  
This can be super useful for custom ops, e.g. using beast etc.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-04 06:55:05 UTC  
> Url: https://github.com/boostorg/cobalt/issues/87#issuecomment-1704713133  

See #90.
