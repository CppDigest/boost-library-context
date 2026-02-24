# #646 - Copy constructor/operator [Closed]

> Username: ksergey  
> Created at: 2017-07-13 07:46:25 UTC  
> Updated at: 2017-07-13 12:35:38 UTC  
> Closed at: 2017-07-13 12:28:55 UTC  
> Url: https://github.com/boostorg/beast/issues/646  

file_posix, file_stdio, file_win32 is copyable.   
I think it's wrong behaviour

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-13 11:32:25 UTC  
> Url: https://github.com/boostorg/beast/issues/646#issuecomment-315051796  

eww..if that's true then its a bug!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-13 11:34:31 UTC  
> Url: https://github.com/boostorg/beast/issues/646#issuecomment-315052201  

Are you sure its copy constructible? This assert does not fire:  
```  
BOOST_STATIC_ASSERT(! std::is_copy_constructible<file_win32>::value);  
```

---

## Comment 3

> Username: ksergey  
> Created at: 2017-07-13 12:28:55 UTC  
> Url: https://github.com/boostorg/beast/issues/646#issuecomment-315063084  

@vinniefalco   
TIL   
> The implicitly-declared copy constructor for class T is undefined if any of the following conditions are true:	  
>T has a user-defined move constructor or move assignment operator;  
  
Sorry for that

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-13 12:35:38 UTC  
> Url: https://github.com/boostorg/beast/issues/646#issuecomment-315064638  

No problem, I had to double check it myself - I have added tests now so I think this was helpful!
