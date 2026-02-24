# #713 - Hard to use the library for URL validation [Closed]

> Username: xim  
> Created at: 2023-03-14 21:42:06 UTC  
> Updated at: 2023-03-16 15:28:19 UTC  
> Closed at: 2023-03-16 15:28:19 UTC  
> Url: https://github.com/boostorg/url/issues/713  

I wanted to validate an externally-input URL using Boost.URL.  
  
The naïve implementation that I assumed would be sufficient wasn't:  
  
```cpp  
auto result = boost::urls::parse_uri(url);  
if (!result.has_value())  
    // handle error  
```  
That apparently lets me parse the URL `https://` and get a result.  
  
After that I tried  
```cpp  
if (!result.has_value() || !result->has_authority())  
    // handle error or missing authority  
```  
The super-interesting is that this returns `has_authority=true`  
  
Is this a bug, am I using the wrong API for URL validation, or do I need to use `host().empty()` instead?  
  
Using boost 1.81.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-14 21:55:34 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468894117  

Can you show the string you are parsing?

---

## Comment 2

> Username: xim  
> Created at: 2023-03-14 21:59:51 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468897820  

> That apparently lets me parse the URL `https://` and get a result.  
  
Sorry, should have just put that straight in the example. I'm parsing incomplete inputs, e.g. `https://`

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-03-14 22:00:58 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468898880  

That is not an incomplete input. It has an authority with a host that happens to be empty. Hosts are allowed to be empty strings.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-03-14 22:02:29 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468900246  

For example look here:  
https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__url_base/set_host.html#url.ref.boost__urls__url_base.set_host.bnf  
![image](https://user-images.githubusercontent.com/1503976/225151336-b44e932d-1cba-48ef-8468-edc490503a62.png)  
  
_reg-name_ is "zero or more of..." which means it can be empty. So host can be empty.

---

## Comment 5

> Username: xim  
> Created at: 2023-03-14 22:06:29 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468904112  

Ah. I understand I guess it makes a world of sense for e.g. `file:///`. It is, however, not a particularly useful HTTP URL. Is there any example of using Boost.URL for more specific URL validation? Would you consider the below test somewhat solid?  
  
```cpp  
auto result = boost::urls::parse_uri(url);  
if (!result.has_value() || !result->host().empty())  
    // handle error or missing authority  
```

---

## Comment 6

> Username: alandefreitas  
> Created at: 2023-03-14 22:06:47 UTC  
> Updated at: 2023-03-14 22:07:42 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468904377  

The library is correct. `https://` is a valid URL.  
  
Have a look at the URL types: https://www.boost.org/doc/libs/master/libs/url/doc/html/url/urls/parsing.html  
  
About the second question, the `https://` has an empty authority. `http:` has no authority.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-03-14 22:08:49 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468906167  

Boost.URL can't know what you consider useful, you have to define that yourself. Make a formal specification of what you require to be in the URL and then use Boost.URL to check if those conditions are met. Boost.URL only guarantees that it will check whether or not the string is syntactically valid according to the ABNF grammar you choose when you select the parsing function.

---

## Comment 8

> Username: xim  
> Created at: 2023-03-14 22:11:10 UTC  
> Url: https://github.com/boostorg/url/issues/713#issuecomment-1468908832  

Thanks. :+1:
