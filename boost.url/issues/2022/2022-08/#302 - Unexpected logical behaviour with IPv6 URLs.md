# #302 - Unexpected logical behaviour with IPv6 URLs [Closed]

> Username: pavel-odintsov  
> Created at: 2022-08-02 19:49:28 UTC  
> Updated at: 2022-08-31 19:53:29 UTC  
> Closed at: 2022-08-03 17:10:12 UTC  
> Url: https://github.com/boostorg/url/issues/302  

Hello!  
  
I've tried parsing IPv6 address with your library and I think it does not behave as I've expected.  
  
Typically IPv6 URLs are represented following way:  
```  
http://[::1]:81/params  
```  
  
When we normally use IPv6 addresses not in URL we do encode them this way:  
```  
::1  
```  
  
I've tried using this code to parse such URL:  
```  
    try {  
        boost::urls::result<boost::urls::url_view> result = boost::urls::parse_uri( full_url );  
  
        if (result.has_error()) {  
            logger << log4cpp::Priority::ERROR << "Cannot parse URL " << full_url;  
            return false;  
        }  
  
        boost::urls::url_view u = result.value();  
  
        host = u.host().to_string();  
  
        // For IPv6 addresses we need to remove wrapping []  
        boost::replace_all(host, "[", "");  
        boost::replace_all(host, "]", "");  
  
        port = u.port();  
        path = u.path().to_string();  
  
        return true;  
    } catch (...) {  
        logger << log4cpp::Priority::ERROR << "Exception triggered during URL parse process for: " << full_url;  
    }  
```  
  
As you may notice I had to add [ and ] stripping code as host() returns IPv6 address in following format: "[::1]".  
  
I think it may be more appropriate to return it as "::1".  
  
What do you think about it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-02 19:50:47 UTC  
> Updated at: 2022-08-02 19:52:15 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1203149774  

I think you should call `u.ipv6_address()` instead, what do you think?  
  
The philosophy of the library is to always offer two ways to interpret fields which have different meaning in generic versus scheme-specific syntax. "host" can be any string, so if you call `host()` we are going to return the string exactly as-is.   
  
If you want to see the host as an ipv6-address, you have to opt-in to it by calling the appropriate function.   
  
This allows `url` and `url_view` to be used with any uri-scheme, without being tied to interpreting host as being any type of IP address.

---

## Comment 2

> Username: pavel-odintsov  
> Created at: 2022-08-02 19:52:38 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1203151541  

It may work but in that case I'll need to know that address is IPv6 one. I'm trying hard to use same logic for IPv4 and IPv6 and it will definitely make IPv6 look snowflake with special logic for it.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-02 19:53:07 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1203151927  

we could add a new function, like `host_address` which does that work for you?

---

## Comment 4

> Username: pavel-odintsov  
> Created at: 2022-08-02 19:58:37 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1203156730  

Let me think about it little bit more.

---

## Comment 5

> Username: pavel-odintsov  
> Created at: 2022-08-03 13:23:26 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1203945712  

I would not say that one day of thinking about this problem changed anything significantly but I decided to check what other languages do and I found Go's example: https://pkg.go.dev/net/url#URL.Hostname So they strip square brackets by default.   
  
I've checked RFC about URL https://www.ietf.org/rfc/rfc3986.txt and it claims that [ ] should not be used in hostname in any other cases but IPv6 address encoding.  
  
I believe stripping by default may be reasonable option.   
  
I'm trying to imagine case why client / user may need "[" or "]" as part of hostname for processing after parsing done and I do not think that it has any applications. Square brackets are only here to help us to parse URL and definitely not a part of address itself.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-08-03 14:21:01 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1204015620  

Well there's a slight problem. This fails:  
```  
url u;  
u.set_encoded_host( "[::]" );  
assert( u.encoded_host() == "[::]" );  
```  
  
A principle of the library is that when you set an encoded component and then retrieve that component you always get the exact same string back.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-08-03 14:23:47 UTC  
> Updated at: 2022-08-03 14:24:00 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1204019030  

The RFC is pretty clear:  
```  
        host        = IP-literal / IPv4address / reg-name  
  
        IP-literal  = "[" ( IPv6address / IPvFuture  ) "]"  
```  
  
The library uses the term "host" as consistently as possible according to the specification. The BNF includes the brackets, so we do as well. I am very open to adding a new function to do what you want:  
  
```  
string_view url_view::encoded_hostname() const noexcept;  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-08-03 14:41:43 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1204041716  

Could you please try this and tell me if it satisfies your use-case? Thanks!  
  
https://github.com/CPPAlliance/url/pull/314

---

## Comment 9

> Username: pavel-odintsov  
> Created at: 2022-08-03 15:34:58 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1204117643  

encoded_hostname() is a great idea, thank you!

---

## Comment 10

> Username: vinniefalco  
> Created at: 2022-08-31 19:53:29 UTC  
> Url: https://github.com/boostorg/url/issues/302#issuecomment-1233357722  

Just a heads up, we are refactoring this API. These are the new functions:  
```  
host_type       host_type()  
std::string     host()  
pct_string_view encoded_host()  
std::string     host_address()  
pct_string_view encoded_host_address()  
ipv4_address    host_ipv4_address()  
ipv6_address    host_ipv6_address()  
string_view     host_ipvfuture()  
  
                set_host_name( string_view )  
                set_host_address( ipv4_address )  
                set_host_address( ipv6_address )  
                set_host_ipvfuture( string_view )  
                set_host( string_view )  
                set_encoded_host( pct_string_view )  
                set_encoded_host_name( pct_string_view )  
```  
  
The one you want is `host_address` (or `encoded_host_address`). This will return the ipv6 address string without brackets, the ipvfuture address string without brackets, the ipv4 address string, or the host reg-name (after applying percent-decoding).
