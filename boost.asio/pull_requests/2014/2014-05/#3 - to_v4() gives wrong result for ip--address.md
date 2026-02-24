# #3 to_v4() gives wrong result for ip::address [Closed]

> Username: mkm85  
> Created at: 2014-05-06 07:55:37 UTC  
> Updated at: 2014-08-06 06:43:34 UTC  
> Closed at: 2014-05-06 09:22:13 UTC  
> Url: https://github.com/boostorg/asio/pull/3  

When using to_v4() on an ip::address_v6 which contains a valid ipv4  
address to_v4() returns a valid ip::address_v4.  
Doing the same on a ip::address which holds an IPv6 address gave a bad  
cast exception.  
The fix is to call to_v4() on the ipv6_address_ if it contains an ipv6  
address.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2014-05-06 09:22:13 UTC  
> Url: https://github.com/boostorg/asio/pull/3#issuecomment-42281964  

Thanks for the patch, but this is by design. An IPv4-mapped IPv6 address is intended for use with an IPv6 socket (i.e. AF_INET6) and not an IPv4 socket (i.e. AF_INET), and hence is not considered an IPv4 address for the purposes of the address class.

---

## Comment 2

> Username: mkm85  
> Created_at: 2014-05-06 09:42:22 UTC  
> Url: https://github.com/boostorg/asio/pull/3#issuecomment-42283367  

Thanks, for the clarification. It's clear from reading the headers again that the intended semantics of  ip::address::to_v4() is completely different from the semantics of ip::address_v6::to_v4().

---

## Comment 3

> Username: chriskohlhoff  
> Created_at: 2014-05-06 10:05:41 UTC  
> Url: https://github.com/boostorg/asio/pull/3#issuecomment-42285112  

Yep, and in the current standards proposal based on Asio's IP addresses these use different names. To extract from a generic address, you use address_cast<>():  
  
```  
template <class T> T address_cast(const address&) noexcept(see below);  
```  
  
and to convert an IPv6-mapped address to IPv4, you use make_address_v4():  
  
```  
address_v4 make_address_v4(v4_mapped_t, const address_v6&);  
```  
  
See the draft attached here https://groups.google.com/a/isocpp.org/d/msg/networking/DCum6Cg3tPc/sBvAKpfKqr8J for details.

---
