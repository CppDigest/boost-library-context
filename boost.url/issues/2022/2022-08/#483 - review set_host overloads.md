# #483 - review set_host overloads [Closed]

> Username: vinniefalco  
> Created at: 2022-08-31 16:21:37 UTC  
> Updated at: 2022-09-02 01:08:27 UTC  
> Closed at: 2022-09-02 01:08:27 UTC  
> Url: https://github.com/boostorg/url/issues/483  

We need to define exactly how set_host and set_encoded_host work. Do they recognize ipv4 and ipv6 addresses? And ipvfuture? Whatever we decide, it needs to be documented and applied consistently.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-31 16:21:57 UTC  
> Url: https://github.com/boostorg/url/issues/483#issuecomment-1233154670  

We should start by making a list of the current host related overloads

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-08-31 16:33:27 UTC  
> Url: https://github.com/boostorg/url/issues/483#issuecomment-1233166633  

`set_authority` and `set_encoded_authority` are part of this API

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-31 17:12:51 UTC  
> Updated at: 2022-08-31 17:13:55 UTC  
> Url: https://github.com/boostorg/url/issues/483#issuecomment-1233204952  

We have   
  
```  
url_base&  
set_host(  
    ipv4_address const& addr);  
  
url_base&  
set_host(  
    ipv6_address const& addr);  
  
url_base&  
set_host(  
    string_view s);  
  
url_base&  
set_encoded_host(  
    string_view s);  
```  
  
then  
  
`set_encoded_host` talks about ipv4, ipv6, and ipvfuture, while `set_host(string_view s)` only talks about ipv4.  
  
So I think it's only logical that `set_host(string_view s)` should consider about ipv4, ipv6, and ipvfuture too.  
  
```  
url_base&  
set_encoded_authority(  
    string_view s);  
```  
  
says nothing about IP addresses in the docs, but it does accept them.  
  
```  
url_base&  
set_authority(  
    string_view s);  
```  
  
doesn't exist.  
  
`IPv4address` has only `unreserved` characters. So in  
  
```  
host        = IP-literal / IPv4address / reg-name  
```  
  
the `/` means the first match otherwise an `IPv4address` could also be a `reg-name`.  
  
So I think the same logic applies to `IP-literal`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-01 02:03:14 UTC  
> Url: https://github.com/boostorg/url/issues/483#issuecomment-1233644802  

```  
host_type       host_type()                 // ipv4, ipv6, ipvfuture, name  
  
std::string     host()                      // return encoded_host().decode_to_string()  
pct_string_view encoded_host()              // return host part, as-is  
std::string     host_address()              // return encoded_host_address().decode_to_string()  
pct_string_view encoded_host_address()      // ipv4, ipv6, ipvfut, or encoded name, no brackets  
  
ipv4_address    host_ipv4_address()         // return ipv4_address or {}  
ipv6_address    host_ipv6_address()         // return ipv6_address or {}  
string_view     host_ipvfuture()            // return ipvfuture or {}  
std::string     host_name()                 // return decoded name or ""  
pct_string_view encoded_host_name()         // return encoded host name or ""  
  
--------------------------------------------------  
  
set_host( string_view )                     // set host part from plain text  
set_encoded_host( pct_string_view )         // set host part from encoded text  
set_host_address( string_view )             // set host from ipv4, ipv6, ipvfut, or plain reg-name string  
set_encoded_host_address( pct_string_view ) // set host from ipv4, ipv6, ipvfut, or encoded reg-name string  
  
set_host_ipv4_address( ipv4_address )       // set ipv4  
set_host_ipv6_address( ipv6_address )       // set ipv6  
set_host_ipvfuture( string_view )           // set ipvfuture  
set_host_name( string_view )                // set name from plain  
set_encoded_host_name( pct_string_view )    // set name from encoded  
```
