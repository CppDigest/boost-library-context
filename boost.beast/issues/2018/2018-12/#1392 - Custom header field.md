# #1392 - Custom header field [Closed]

> Username: ivan-ushakov  
> Created at: 2018-12-27 22:09:31 UTC  
> Updated at: 2018-12-29 14:23:56 UTC  
> Closed at: 2018-12-29 14:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1392  

In current code there is no way to provide custom header field, only field from enum. I understand that this was done by RFC, but sometimes you need to provide session token, or some other field not from enum. How to do this?  
  
With current code the only way I can do this is to add my custom field to enum and modify some other places to have it working. In other libraries there is no such restriction.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-28 01:37:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1392#issuecomment-450269350  

What's wrong with using this overload?  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_fields/insert/overload2.html

---

## Comment 2

> Username: ivan-ushakov  
> Created at: 2018-12-28 06:44:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1392#issuecomment-450301225  

@vinniefalco I already tried to use this one but internally it tries to search in table to find enum value for given string (see `string_to_field`).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-28 08:28:12 UTC  
> Updated at: 2018-12-28 08:28:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1392#issuecomment-450316746  

It works perfectly fine for me (and many other people). No idea why it doesn't work for you, This, for example, does the right thing:  
```  
        f.insert("Custom", "Header");  
        BOOST_ASSERT(f["Custom"] == "Header");  
```  
  
What version of Boost are you on?

---

## Comment 4

> Username: ivan-ushakov  
> Created at: 2018-12-29 14:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1392#issuecomment-450496466  

Yes, you are right. I was confused by enum.
