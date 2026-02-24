# #711 - boost::urls::ipv6_address does not support scope id [Closed]

> Username: frogarian  
> Created at: 2023-03-13 14:22:48 UTC  
> Updated at: 2023-07-20 17:13:33 UTC  
> Closed at: 2023-07-20 17:13:33 UTC  
> Url: https://github.com/boostorg/url/issues/711  

Unlike in `boost::asio::ip::address_v6`, in `boost::urls::ipv6_address` scope ids don't seem to be supported.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-13 14:24:42 UTC  
> Url: https://github.com/boostorg/url/issues/711#issuecomment-1466241105  

Scope ID is not in rfc3986, but this RFC does update it: https://www.rfc-editor.org/rfc/rfc6874

---

## Comment 2

> Username: frogarian  
> Created at: 2023-03-13 14:30:45 UTC  
> Url: https://github.com/boostorg/url/issues/711#issuecomment-1466251469  

So it won't be supported in  `Boost.URL` anytime soon?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-03-13 14:45:08 UTC  
> Url: https://github.com/boostorg/url/issues/711#issuecomment-1466278208  

If later RFCs update rfc3986 as this one appears to do then we should support it

---

## Comment 4

> Username: alandefreitas  
> Created at: 2023-07-19 15:05:51 UTC  
> Url: https://github.com/boostorg/url/issues/711#issuecomment-1642265733  

> Unlike in boost::asio::ip::address_v6  
  
I'm trying to implement this thing but what `asio` supports is an extra `std::uint32_t` in the ipv6 address to store the [scope_id](https://www.boost.org/doc/libs/1_82_0/doc/html/boost_asio/reference/ip__address_v6/scope_id.html) while what URLs in [RFC6874](https://datatracker.ietf.org/doc/html/rfc6874) support is an extra Zone ID of any size in front of the IPv6:  
  
```  
      IP-literal = "[" ( IPv6address / IPv6addrz / IPvFuture  ) "]"  
      ZoneID = 1*( unreserved / pct-encoded )  
      IPv6addrz = IPv6address "%25" ZoneID  
```  
  
The problem is there's no deterministic relationship between the two. The mapping from the `Zone ID` and the 32-bit `scope_id`  is obtained from an operating system API that maps the zone string to its corresponding numeric value.  
  
So we can include the same `scope_id` overloads in our `ipv6address` but there's no way to determine this value from the `ZoneID` string. Or we can just store the `ZoneID` in `ipv6address` and not deal with the conversion but that would imply `ipv6address` carrying a whole `std::string` or, even worse, `ipv6address` and `ipv6address_view` variants.   
  
Another option is just accepting the `ZoneID` as valid input but not changing anything in `ipv6address`: it would just store the addresses and have a `scope_id` field that can be set but it's always zero by default.
