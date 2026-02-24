# #910 - Add [[nodiscard]] attribute to the boost::span::subspan [Open]

> Username: ujos  
> Created at: 2024-05-22 13:51:06 UTC  
> Updated at: 2024-05-22 13:51:06 UTC  
> Url: https://github.com/boostorg/boost/issues/910  

Could you add the `[[nodiscard]]` attribute to the `boost::span::subspan` function. I tend to forget to store the returned value:  
  
```cpp  
buffer_.subspan(bytesTransferred);  
```  
... instead of  
  
```cpp  
buffer_ = buffer_.subspan(bytesTransferred);  
```
