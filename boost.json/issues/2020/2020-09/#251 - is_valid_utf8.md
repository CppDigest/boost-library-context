# #251 - is_valid_utf8 [Closed]

> Username: vinniefalco  
> Created at: 2020-09-02 13:23:03 UTC  
> Updated at: 2022-05-25 12:06:56 UTC  
> Closed at: 2022-05-25 12:06:56 UTC  
> Url: https://github.com/boostorg/json/issues/251  

This could be of use  
```  
bool is_valid_utf8( string_view s);  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-02 13:30:57 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685738637  

This one could return `s` or throw:  
```  
string_view valid_utf8( string_view s );  
```

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-09-02 15:30:08 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685813969  

You couldn't have both though. I prefer the first.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-02 15:34:16 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685816746  

Of course you can have both. The first one is good, but so is the second one, as you can use it in expressions:  
```  
value jv = valid_utf8( "\xff" ); // throws  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-02 15:37:35 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685818859  

Nothing actionable here yet

---

## Comment 5

> Username: pdimov  
> Created at: 2020-09-02 15:39:22 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685820074  

I would expect a function returning string_view to return the longest valid UTF-8 prefix, not identity.

---

## Comment 6

> Username: sdkrystian  
> Created at: 2020-09-02 15:40:41 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-685820985  

You can't have both; functions cannot be overloaded based on return type.

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-05-25 11:12:52 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-1137111433  

Is this in scope, though?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-05-25 12:06:56 UTC  
> Url: https://github.com/boostorg/json/issues/251#issuecomment-1137158557  

No one has asked, so no
