# #117 - Description heading weirdness [Closed]

> Username: vinniefalco  
> Created at: 2021-11-03 17:44:27 UTC  
> Updated at: 2022-01-09 21:58:32 UTC  
> Closed at: 2022-01-09 21:58:32 UTC  
> Url: https://github.com/boostorg/docca/issues/117  

The heading shouldn't be there, but also the next heading is on the same line?  
![image](https://user-images.githubusercontent.com/1503976/140163611-8ed47db1-b2ba-4643-afce-d4e0d8c143c4.png)  
  
This is the source:  
```  
struct ipv6_address  
{  
    /** Construct from an array of bytes.  
  
        @param bytes The value to construct from.  
    */  
    ipv6_address(  
        bytes_type const& bytes) noexcept;;  
};  
```
