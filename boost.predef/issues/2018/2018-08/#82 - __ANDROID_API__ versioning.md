# #82 - __ANDROID_API__ versioning? [Closed]

> Username: jeking3  
> Created at: 2018-08-15 17:55:54 UTC  
> Updated at: 2018-08-15 17:56:35 UTC  
> Closed at: 2018-08-15 17:56:35 UTC  
> Url: https://github.com/boostorg/predef/issues/82  

Is there support in predef to handle conditionals around this android macro today?  
```  
#if __ANDROID_API__ >= 28  
int getentropy(void* __buffer, size_t __buffer_size) __wur __INTRODUCED_IN(28);  
  
ssize_t getrandom(void* __buffer, size_t __buffer_size, unsigned int __flags) __wur __INTRODUCED_IN(28);  
#endif /* __ANDROID_API__ >= 28 */  
```  
  
See: https://github.com/boostorg/uuid/issues/76
