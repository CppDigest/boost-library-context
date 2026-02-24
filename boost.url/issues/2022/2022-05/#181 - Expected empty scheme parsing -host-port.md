# #181 - Expected empty scheme parsing "host:port" [Closed]

> Username: kalikin  
> Created at: 2022-05-18 15:38:55 UTC  
> Updated at: 2022-05-18 19:47:27 UTC  
> Closed at: 2022-05-18 19:47:27 UTC  
> Url: https://github.com/boostorg/url/issues/181  

Hello. I tried to parse a string without a scheme like `localhost:5555` and expected an empty scheme, but instead got an empty host and port. Parsing  `127.0.0.1:5555` and `[::1]:5555` fails. Is this expected behavior? Are these not valid URIs? Thank you.  
```cpp  
auto result = parse_uri("localhost:5555");  
auto hasValue = result.has_value(); // true  
auto url = result.value();  
auto hasScheme = url.has_scheme(); // expect false, but got true  
auto scheme = url.scheme(); // expect "", but got "localhost"  
auto host = url.host(); // expect "localhost", but got ""  
auto port = url.port_number(); // expect "5555", but got 0  
```

---

## Comment 1

> Username: sehe  
> Created at: 2022-05-18 16:05:28 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130209352  

Looks like a misparse indeed. Here's my repro on develop:  
  
```c++  
#include <boost/url.hpp>  
#include <boost/url/src.hpp>  
#include <iostream>  
  
#define INSPECT(x) std::cout << #x << ": " << std::boolalpha << (x) << std::endl;  
int main() {  
    auto result = boost::urls::parse_uri("localhost:5555");  
    std::cout << "error: " << result.error().message() << std::endl;  
    INSPECT(result.has_value()); // true  
  
    auto url = result.value();  
    INSPECT(url.has_scheme());   // expect false, but got true  
    INSPECT(url.scheme());       // expect "", but got "localhost"  
    INSPECT(url.host());         // expect "localhost", but got ""  
    INSPECT(url.port_number());  // expect "5555", but got 0  
}  
```  
  
Prints  
  
```  
error: Success  
result.has_value(): true  
url.has_scheme(): true  
url.scheme(): localhost  
url.host():  
url.port_number(): 0  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-18 16:06:20 UTC  
> Updated at: 2022-05-18 16:06:41 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130210324  

Broseph, you need to be using `parse_authority` not `parse_uri`. The library behavior of the code samples above is correct.

---

## Comment 3

> Username: sehe  
> Created at: 2022-05-18 16:11:23 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130215515  

Easy mistake though. I noted this earlier when you mentioned secondary parse-functions in cppslack.  
  
I must not be alone in expecting these functions to be "off-limits" as implementation details. It would be nice if the library could DWIM instead of saying "You should have been more explicit about your question".  
  
In particular, this use-case could well be when untrusted user input is being verified, and I consider the approach "You have to parse as a URI, but then compare with parsing as just authority to see whether you get ambiguous results" a Very Poor(TM) programming interface.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-05-18 16:15:39 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130219684  

`parse_uri` parses a URI, which has a precise syntax. "localhost:5555" is in fact a valid URI with scheme "localhost" and path "5555". How is the library supposed to know that you wanted an authority?

---

## Comment 5

> Username: sehe  
> Created at: 2022-05-18 16:17:40 UTC  
> Updated at: 2022-05-18 16:18:16 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130221679  

I'm not saying it was wrong. I'm saying it was surprising, and I wondering how to fill that common use-case I described: validating user input.  
  
I suppose people should check that the parse succeeded *and* got at least a scheme and authority. Wondering whether I'm still missing edge cases there (and that leads to "do we need to educate users via documentation/examples"?)

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-05-18 16:25:45 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130229653  

I can't think of anything more we can do about it

---

## Comment 7

> Username: sehe  
> Created at: 2022-05-18 16:32:35 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130236199  

Checking the [RFC](https://datatracker.ietf.org/doc/html/rfc3986#section-3.2) indeed users should additionally to check `has_authority()`.  
  
In laymen's terms, this guarantees that the parsed URI contained `//`.  
  
> I can't think of anything more we can do about it  
  
We can document use-cases that casual users may not be aware of. This is what I consider "educating users", and is a basic requirement for responsible library use, IYAM.   
  
What about a sample showing basic URL validation? I can propose one

---

## Comment 8

> Username: sehe  
> Created at: 2022-05-18 16:37:46 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130240924  

Just as a sanity check, I compared with e.g. python3:  
  
```  
from urllib.parse import urlparse  
urlparse('http') # ParseResult(scheme='', netloc='', path='http', params='', query='', fragment='')  
urlparse('http:') # ParseResult(scheme='http', netloc='', path='', params='', query='', fragment='')  
urlparse('localhost:5555') # ParseResult(scheme='', netloc='', path='localhost:5555', params='', query='', fragment='')  
urlparse('localhost:') # ParseResult(scheme='localhost', netloc='', path='', params='', query='', fragment='')  
```  
  
I haven't nearly spent enough time reading the RFC to rule out what implementation is more correct

---

## Comment 9

> Username: vinniefalco  
> Created at: 2022-05-18 16:41:47 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130244444  

What happens when parsing these strings:  
```  
xyz:5555  
xyz:etc  
```

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-05-18 16:46:09 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130249533  

@kalikin, you can check this table to find what function you need:  
  
Function | Grammar Element | Grammar Rule  
-- | -- | --  
[parse_absolute_uri](https://master.url.cpp.al/url/ref/boost__urls__parse_absolute_uri.html) | [absolute-URI](https://datatracker.ietf.org/doc/html/rfc3986#section-4.3) | absolute-URI    = scheme ":" hier-part [ "?" query ]  
[parse_relative_ref](https://master.url.cpp.al/url/ref/boost__urls__parse_relative_ref.html) | [relative-ref](https://datatracker.ietf.org/doc/html/rfc3986#section-4.2) | relative-ref    = relative-part [ "?" query ] [ "#" fragment ]  
[parse_uri](https://master.url.cpp.al/url/ref/boost__urls__parse_uri.html) | [URI](https://datatracker.ietf.org/doc/html/rfc3986#section-3) | URI             = scheme ":" hier-part [ "?" query ] [ "#" fragment ]  
[parse_uri_reference](https://master.url.cpp.al/url/ref/boost__urls__parse_uri_reference.html) | [URI-reference](https://datatracker.ietf.org/doc/html/rfc3986#section-4.1) | URI-reference   = URI / relative-ref  
  
As you can see, `"localhost:5555"` is not a valid `URI` because this would require a scheme. This is a common mistake because HTTP URLs are very common and _HTTP_ normalization would include the "http" scheme and port 80 for us when they're missing, among other things. But the URL protocol cannot assume a specific scheme or port.  
  
In your case, the correct parsing function depends on what you are trying to achieve. You might have a relative-ref or just an authority_view.  
  
- https://master.url.cpp.al/url/ref/boost__urls__parse_relative_ref.html  
- https://master.url.cpp.al/url/parsing/authority.html#url.parsing.authority.authority_view  
  
The complete rules for URIs are in https://master.url.cpp.al/url/parsing/url.html

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-05-18 16:46:17 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130249665  

I see, `http` and various other well-known schemes are treated as a special case:  
https://github.com/python/cpython/blob/ed75d2183f9084583e2ebe9229c52711d4a62f1e/Lib/urllib/parse.py#L47  
  
I think both of the examples I gave above will be treated as a path, since xyz is not a known scheme.

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-05-18 16:49:36 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130252501  

By the way, Boost.URL and javascript's URL have the same results here:  
  
```  
> new URL('localhost:5555')  
URL {  
  href: 'localhost:5555',  
  origin: 'null',  
  protocol: 'localhost:',  
  username: '',  
  password: '',  
  host: '',  
  hostname: '',  
  port: '',  
  pathname: '5555',  
  search: '',  
  searchParams: URLSearchParams {},  
  hash: ''  
}  
```

---

## Comment 13

> Username: sehe  
> Created at: 2022-05-18 16:55:17 UTC  
> Updated at: 2022-05-18 16:56:45 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130257438  

@vinniefalco Well (╯°□°）╯︵ ┻━┻  
  
![image](https://user-images.githubusercontent.com/324097/169098914-248eaae9-d9ae-4f27-97b6-02af3d8fed6e.png)  
  
I think it's fair to say that the situation is fundamentally ambiguous UNLESS you assume left-to-right greedy parsing (which apparently Boost.URL and javascript do). Actually, that's a very good strategy, which is easy to document.  
  
If it turns out that the RFC has normative wording specifying this behaviour, all the better

---

## Comment 14

> Username: vinniefalco  
> Created at: 2022-05-18 17:01:31 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130262900  

I don't understand why `urlparse` is treating `xyz:5555` as a path. It looks broken to me.

---

## Comment 15

> Username: alandefreitas  
> Created at: 2022-05-18 17:03:32 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130264761  

I don't think it's ambiguous at all. In  
  
```  
URI             = scheme ":" hier-part [ "?" query ] [ "#" fragment ]  
```  
  
we have scheme "localhost" and hier-part / path "5555".  
  
The aggregation of user expectations might be ambiguous, but RFC 3986 is not.

---

## Comment 16

> Username: alandefreitas  
> Created at: 2022-05-18 18:23:50 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130364212  

As more issues of this kind are created, I think the only thing actionable here is to add something here and there in the docs about normalizations this library won't do, such as returning port 80 when the port is missing.   
  
For each normalization the library doesn't do, we would need to mention where the expectation usually comes from (like HTTP normalization instead of URL normalization), why this is not in the URL spec, and how to do it properly.   
  
Unfortunately, in this case, I can't explain where the expectation that "localhost:2342" would be normalized to "http://localhost:2342/" comes from. I know it's something browsers do, but I don't think this is part of the HTTP normalization spec.

---

## Comment 17

> Username: sehe  
> Created at: 2022-05-18 18:29:16 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130368865  

Returning port 80 is not a normalization. Also, it's incorrect, as it would break some servers that would not accept `server:80` in the Host header, and some that specifically require it (binance.com API, looking at you again). That's clearly domain logic.  
  
It's more important to simply document that the URI may be more general than people may expect, so they might need additional validation after a succesful parse.  
  
> in this case, I can't explain where the expectation that "localhost:2342" would be normalized to "http://localhost:2342/" comes from  
  
You and me both, because you're literally the first person to suggest that expectation. What was unexpected was that `localhost` be parsed as the scheme. Nobody said /anything/ about assuming defaults or even normalizing

---

## Comment 18

> Username: alandefreitas  
> Created at: 2022-05-18 19:34:06 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130437674  

> Returning port 80 is not a normalization.   
  
It depends on what you mean by normalization. The URL spec defines normalization and comparisons with the same rules, where comparison is just string comparison after normalization. Then there's String Comparison < Syntax-Based Normalization/Comparison < Scheme-Based Normalization/Comparison < Protocol-Based Normalization/Comparison.  
  
The URL normalization in this library is and can only be Syntax-Based, because Scheme-Based normalization/comparison would depend on the scheme. So if you mean syntax-based normalization, then no, port 80 should not be implied.   
  
Then the HTTP spec defines its own normalization/comparison rules, which would be called Scheme-Based Normalization in the URL spec. So if you mean HTTP scheme-based normalization, then yes, port 80 should be assumed:  
  
> The default port is TCP 80 [[19](https://datatracker.ietf.org/doc/html/rfc2616#ref-19)], but other ports can be used.  
> (...)  
> A port that is empty or not given is equivalent to the default port for that URI-reference;  
> (...)  
> For example, the following three URIs are equivalent:  
>    [http://abc.com:80/~smith/home.html](http://abc.com/~smith/home.html)  
>    [http://ABC.com/%7Esmith/home.html](http://abc.com/~smith/home.html)  
>    [http://ABC.com:/%7esmith/home.html](http://abc.com/~smith/home.html)  
  
It's part of HTTP URL normalization but not URL normalization.  
  
> Also, it's incorrect, as it would break some servers that would not accept server:80 in the Host header, and some that specifically require it (binance.com API, looking at you again). That's clearly domain logic.  
  
That's an unrelated issue. Whether server A or server B or API C require server:80 or server has nothing to do with what correct according to the URL and HTTP RFC. Even what the URL spec says is not the same as the HTTP spec says about it.  
  
> It's more important to simply document that the URI may be more general than people may expect, so they might need additional validation after a succesful parse.  
  
It might be a little more complicated than that, because some scheme-based normalization rules allow inputs that are not valid URIs according to the URL spec.   
  
For instance, your browser will put `http:` in front of `www.boost.org` for you. This normalization is not a guess. It's part of some other spec.   
  
The problem is `www.boost.org` is not a valid URI according to RFC3986. So it's not a matter of additional validation because `www.boost.org` would return an error in this library.   
  
So it's a little more intrusive because we would need some other functions for that, like `parse_http_url` or something.  
  
> You and me both, because you're literally the first person to suggest that expectation.   
  
I don't know where the expectation comes from _officially_. I'm saying I literally don't know from which _specification_ it is.   
  
The informal user expectation/intuition is quite easy to explain. Just type "localhost:2342" in your browser and it will normalize it to "http://localhost:2342/" for you.  
  
> What was unexpected was that localhost be parsed as the scheme.   
  
Yes. Because people just type "localhost:2342" in their browsers and it normalizes to "http://localhost:2342/" for them, where "localhost:2342" is the host and port. Therefore, they assume parse_uri("localhost:2342") would also give them a host and port, ignoring the browser did an extra step for them. This expectation is against the URL spec.  
  
> Nobody said /anything/ about assuming defaults or even normalizing  
  
All of the comments here are about expectations people create because of normalizations that happen here and there. We have issues related to that all the time.  
  
If there's no intention to discuss the effect of expectations and normalizations on the documentation, there's nothing else to discuss beyond this table, which is self-explanatory, and the issue can be closed:  
  
Function | Grammar Element | Grammar Rule  
-- | -- | --  
[parse_absolute_uri](https://master.url.cpp.al/url/ref/boost__urls__parse_absolute_uri.html) | [absolute-URI](https://datatracker.ietf.org/doc/html/rfc3986#section-4.3) | absolute-URI    = scheme ":" hier-part [ "?" query ]  
[parse_relative_ref](https://master.url.cpp.al/url/ref/boost__urls__parse_relative_ref.html) | [relative-ref](https://datatracker.ietf.org/doc/html/rfc3986#section-4.2) | relative-ref    = relative-part [ "?" query ] [ "#" fragment ]  
[parse_uri](https://master.url.cpp.al/url/ref/boost__urls__parse_uri.html) | [URI](https://datatracker.ietf.org/doc/html/rfc3986#section-3) | URI             = scheme ":" hier-part [ "?" query ] [ "#" fragment ]  
[parse_uri_reference](https://master.url.cpp.al/url/ref/boost__urls__parse_uri_reference.html) | [URI-reference](https://datatracker.ietf.org/doc/html/rfc3986#section-4.1) | URI-reference   = URI / relative-ref

---

## Comment 19

> Username: sehe  
> Created at: 2022-05-18 19:43:33 UTC  
> Updated at: 2022-05-18 19:43:45 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130462816  

Let's try not to waste more time explaining imputed expectations.  
  
This issue *was* about expectations, not why they happen.  
  
If that table is from the docs, indeed close this issue, because nothing should be done.

---

## Comment 20

> Username: alandefreitas  
> Created at: 2022-05-18 19:47:27 UTC  
> Url: https://github.com/boostorg/url/issues/181#issuecomment-1130469522  

We see issues of this kind very often. It's not a complete waste of time to discuss how we can improve the docs to avoid these. But yes, we can just keep track of new issues of this kind as they appear.
