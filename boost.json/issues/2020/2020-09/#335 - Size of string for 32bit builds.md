# #335 - Size of string for 32bit builds [Closed]

> Username: maximilianriemensberger  
> Created at: 2020-09-14 18:37:22 UTC  
> Updated at: 2020-09-14 20:09:06 UTC  
> Closed at: 2020-09-14 20:09:06 UTC  
> Url: https://github.com/boostorg/json/issues/335  

If I'm not mistaken the string type in this library has 12 bytes in size for 32bit builds due to the key_string union member, see  https://github.com/CPPAlliance/json/blob/fc7b1c6fd229e884df09fd45c64c6102d2aa129b/include/boost/json/detail/string_impl.hpp#L80  
  
Considering that there are careful checks for the sbo and pointer member that they are exactly 8 byte sized, this feels rather odd. Also for 64 bit builds, all three are 16 bytes.  
  
Was that intended?  An if so, why waste those 4 chars in the sbo version?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-14 18:45:16 UTC  
> Updated at: 2020-09-14 18:47:02 UTC  
> Url: https://github.com/boostorg/json/issues/335#issuecomment-692241905  

I'm not sure if this is a problem.  
  
The current version:  
```  
boost.json.zsizes  
sizeof(alignof)  
  object        == 12 (4)  
    value_type  == 32 (8)  
  array         == 12 (4)  
  string        == 16 (4)  
  value         == 16 (8)  
  serializer    == 72  
  parser        == 120  
```  
  
The old version:  
```  
boost.json.zsizes  
sizeof(alignof)  
  object        == 12 (4)  
    value_type  == 32 (8)  
  array         == 12 (4)  
  string        == 16 (4)  
  value         == 16 (8)  
  serializer    == 60  
  parser        == 120  
```  
  
The sizes haven't changed.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-14 19:11:13 UTC  
> Url: https://github.com/boostorg/json/issues/335#issuecomment-692255849  

Yes it looks like we are missing out on 4 characters worth of small buffer for the 32-bit builds!!!
