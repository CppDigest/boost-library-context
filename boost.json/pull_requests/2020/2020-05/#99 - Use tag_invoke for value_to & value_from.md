# #99 Use tag_invoke for value_to & value_from [Closed]

> Username: sdkrystian  
> Created at: 2020-05-26 19:04:58 UTC  
> Updated at: 2020-05-28 00:31:15 UTC  
> Closed at: 2020-05-28 00:31:15 UTC  
> Url: https://github.com/boostorg/json/pull/99  



---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-05-26 19:06:19 UTC  
> Url: https://github.com/boostorg/json/pull/99#issuecomment-634218466  

@pdimov (can't request you for review so I have to tag you)

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-05-26 19:07:27 UTC  
> Url: https://github.com/boostorg/json/pull/99#issuecomment-634219078  

This needs tests and coverage before being reviewed

---

## Comment 3

> Username: sdarwin  
> Created_at: 2020-05-27 21:35:28 UTC  
> Url: https://github.com/boostorg/json/pull/99#issuecomment-634954603  

Hey @sdkrystian ,  
  
Documentation previews aren't building. It looks like test/snippets.cpp should be modified:  
  
```  
//[snippet_exchange_5  
// VFALCO TODO  
```  
  
change to:  
  
```  
//[snippet_exchange_5  
// VFALCO TODO  
//]  
```

---

## Comment 4

> Username: sdkrystian  
> Created_at: 2020-05-27 21:36:43 UTC  
> Url: https://github.com/boostorg/json/pull/99#issuecomment-634955189  

@sdarwin Good catch, thanks!

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-05-27 21:48:09 UTC  
> Url: https://github.com/boostorg/json/pull/99#issuecomment-634960329  

An automated preview of the documentation is available at [http://99.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](http://99.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---
