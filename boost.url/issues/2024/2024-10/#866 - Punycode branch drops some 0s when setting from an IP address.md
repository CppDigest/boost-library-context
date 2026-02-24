# #866 - Punycode branch drops some 0s when setting from an IP address [Closed]

> Username: MitchellBot  
> Created at: 2024-10-03 21:59:34 UTC  
> Updated at: 2026-01-20 21:28:55 UTC  
> Closed at: 2026-01-20 21:28:55 UTC  
> Url: https://github.com/boostorg/url/issues/866  

We're using the punycode branch to parse URIs and recently stumbled upon an issue where IP addresses with multiple 0s will drop some of them during parsing.  
  
For instance, given the IP address 192.168.108.100, if you either `set_host` or `set_host_address` and then re-print the object, it will come out as 192.168.18.10.  
  
Example:  
```c++  
    boost::urls::url someUrl;  
    someUrl.set_host("192.168.108.100");  
    std::cout << "URL is: " << someUrl.c_str() << std::endl;  
```  
  
Output:  
`URL is: //192.168.18.10`  
  
Interestingly, even using an ipv4 object directly doesn't work:  
```c++  
        boost::urls::url someUrl;  
        someUrl.set_host_ipv4(boost::urls::ipv4_address("192.168.108.100"));  
        std::cout << "Boost url (by IP) is: " << someUrl.c_str() << std::endl;  
```  
  
Output:  
`Boost url (by IP) is: //192.168.18.10`  
  
I see that the punycode branch hasn't been updated in quite some time, is it possible that this is already known and has been fixed in `develop`?

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-10-04 13:53:19 UTC  
> Url: https://github.com/boostorg/url/issues/866#issuecomment-2393766003  

Punycode was a short experiment that’s discontinued. It never made it to develop.   
  
What’s your use case for punycode?

---

## Comment 2

> Username: MitchellBot  
> Created at: 2024-10-04 15:09:08 UTC  
> Url: https://github.com/boostorg/url/issues/866#issuecomment-2393926600  

After discussing with my colleagues no one seems to recollect choosing that branch to begin with. It may have been mistakenly downloaded.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2024-10-04 22:49:05 UTC  
> Url: https://github.com/boostorg/url/issues/866#issuecomment-2394762784  

I see. Yes. Punycode never made it to Boost. This punycode implementation is probably not even compatible with the latest version of Boost.URL.
