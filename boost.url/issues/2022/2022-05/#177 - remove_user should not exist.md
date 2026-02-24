# #177 - remove_user should not exist [Closed]

> Username: alandefreitas  
> Created at: 2022-05-17 18:47:57 UTC  
> Updated at: 2022-05-19 03:48:39 UTC  
> Closed at: 2022-05-19 03:48:39 UTC  
> Url: https://github.com/boostorg/url/issues/177  

`url::remove_user` should not exist, as the `user` subcomponent is not optional.  
  
```  
authority       = [ userinfo "@" ] host [ ":" port ]  
userinfo        = user [ ":" [ password ] ]  
```  
  
`url::remove_authority` and `url::remove_password` are still fine.  
  
The current logic also seems problematic as an equivalence to `set_user("")`:   
  
https://github.com/CPPAlliance/url/blob/9e3bc6c423afff0b4ee3d9553db8211037086a63/include/boost/url/impl/url.ipp#L406
