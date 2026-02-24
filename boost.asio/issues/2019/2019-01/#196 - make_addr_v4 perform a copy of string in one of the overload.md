# #196 - make_addr_v4 perform a copy of string in one of the overload [Closed]

> Username: JohnCoconut  
> Created at: 2019-01-16 06:52:31 UTC  
> Updated at: 2020-12-30 00:59:43 UTC  
> Closed at: 2020-12-30 00:59:42 UTC  
> Url: https://github.com/boostorg/asio/issues/196  

https://github.com/boostorg/asio/blob/a6008b6427ddfc3222f83c53030c34c802c53b59/include/boost/asio/ip/impl/address_v4.ipp#L193  
  
Code copied below,  
  
```cpp  
address_v4 make_address_v4(string_view str)  
{  
  return make_address_v4(static_cast<std::string>(str));  
}  
  
address_v4 make_address_v4(string_view str,  
    boost::system::error_code& ec)  
{  
  return make_address_v4(static_cast<std::string>(str), ec);  
}  
```  
  
The following expression,  
```cpp   
static_cast<std::string>(str)  
```  
construct a new string, copying the contents from string_view.  
  
It's better to use the `const char*` overload by calling  
```cpp  
make_address_v4(str.data());  
```

---

## Comment 1

> Username: viccpp  
> Created at: 2019-01-24 09:48:55 UTC  
> Url: https://github.com/boostorg/asio/issues/196#issuecomment-457134096  

`make_address_v4` implementation eventually needs null-terminated string. `std::string` is needed only to add `\0` to the `string_view` which is just pointer+size

---

## Comment 2

> Username: JohnCoconut  
> Created at: 2019-01-25 04:29:58 UTC  
> Url: https://github.com/boostorg/asio/issues/196#issuecomment-457452163  

@2underscores-vic Yes, in current implementation, it  deals with cases in which `string_view` does not end with a `\0`. But if it is a view of a complete string, the copy isn't necessary.   
  
Personally I feel the interface is confusing. I wouldn't expect  `string_view` overload will **always** create a copy. If a copy is _optional_, why don't we let the user decide it?   
  
If the user knows a `string_view sv` is created from the following way,  
  
```cpp  
string s = "1.1.1.1";  
string_view sv{s};  
```  
the user can call call `sv.data()` directly. If `sv` is only a part of string, the user can create a string explicitly. Current `string_view` overload takes away user's freedom to do so.  
  
If current implementation is intended, I hope it is emphasized in the documentation.

---

## Comment 3

> Username: viccpp  
> Created at: 2019-01-25 06:55:09 UTC  
> Url: https://github.com/boostorg/asio/issues/196#issuecomment-457474660  

> Personally I feel the interface is confusing. I wouldn't expect string_view overload will always create a copy.   
  
Agree on this

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 00:59:41 UTC  
> Url: https://github.com/boostorg/asio/issues/196#issuecomment-752291123  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#670](https://github.com/chriskohlhoff/asio/issues/670).
