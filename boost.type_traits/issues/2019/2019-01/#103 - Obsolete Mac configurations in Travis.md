# #103 - Obsolete Mac configurations in Travis [Closed]

> Username: pdimov  
> Created at: 2019-01-05 23:51:02 UTC  
> Updated at: 2019-01-08 09:08:08 UTC  
> Closed at: 2019-01-08 09:08:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/103  

The `xcode8.2`, `xcode8.1` configurations say  
  
```  
 This job was configured to run on an OS X image that was retired on November 28, 2017. It was routed to our Xcode 8.3 image.  
```  
  
(but the 8.3 part is wrong and they actually use the default 9.4.)  
  
`xcode8.0` doesn't exist at all, and also falls back to the default. Should be `xcode8`.  
  
`xcode6.4` says  
  
```  
Running builds with Xcode 6.4 in Travis CI is deprecated and will be removed in January 2019.  
```  
  
See https://docs.travis-ci.com/user/reference/osx#os-x-version

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-08 09:08:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/103#issuecomment-452225246  

Fixed in develop.
