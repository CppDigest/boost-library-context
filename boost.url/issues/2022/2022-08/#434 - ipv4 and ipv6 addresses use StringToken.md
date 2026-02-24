# #434 - ipv4 and ipv6 addresses use StringToken [Closed]

> Username: vinniefalco  
> Created at: 2022-08-22 00:23:18 UTC  
> Updated at: 2022-09-15 19:48:45 UTC  
> Closed at: 2022-09-15 19:48:45 UTC  
> Url: https://github.com/boostorg/url/issues/434  

we should make `to_string` not a template, maybe rename it to `to_std_string` and just return `std::string`. And it needs to use StringToken

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-11 23:47:30 UTC  
> Url: https://github.com/boostorg/url/issues/434#issuecomment-1243070759  

This should use StringToken
