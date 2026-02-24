# #150 - Miss a ')' in the file  boost/type_traits/is_empty.hpp [Closed]

> Username: zouhbwll  
> Created at: 2020-12-25 06:44:01 UTC  
> Updated at: 2020-12-25 16:56:56 UTC  
> Closed at: 2020-12-25 16:56:17 UTC  
> Url: https://github.com/boostorg/type_traits/issues/150  

PC:  Ubuntu 20.04  
Boost: 1.71  
===============================  
  
Recently, I had met:  
>/usr/include/boost/type_traits/is_empty.hpp:78: Parenthesis/brace mismatch between #if and #else branches; using #if branch  
  
![2020-12-23 19-16-00屏幕截图](https://user-images.githubusercontent.com/33795086/103123319-e682e280-46be-11eb-87ab-44473abac05f.png)  
  
It was because of missing the  `)` of  ` BOOST_STATIC_CONSTANT`.  
![2020-12-23 19-14-34屏幕截图](https://user-images.githubusercontent.com/33795086/103123409-38c40380-46bf-11eb-966c-919c6fed72c4.png)  
  
===============================  
  
I can not find this file in this github, so I open this issue.  
Maybe this issue can do some help  :)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-25 16:56:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/150#issuecomment-751271346  

Wow, that bug has been present since the dawn of time!  Fixed in develop.
