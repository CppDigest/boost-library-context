# #742 - Consider allowing non urlencoded brackets for firefox [Closed]

> Username: mserdarsanli  
> Created at: 2023-04-23 00:33:51 UTC  
> Updated at: 2023-04-27 20:07:10 UTC  
> Closed at: 2023-04-27 20:07:10 UTC  
> Url: https://github.com/boostorg/url/issues/742  

First some code for testing what boost/url does: I've tested with boost 1.81 and boost 1.82  
  
```  
#include <iostream>  
#include <boost/url/params_view.hpp>  
#include <boost/url/parse.hpp>  
#include <boost/url/url.hpp>  
  
  
void test_url(const char *origin_form)  
{  
    std::cerr << origin_form << "\n";  
    boost::urls::result<boost::urls::url_view> purl = boost::urls::parse_origin_form(origin_form);  
    if (!purl)  
    {  
        std::cerr << "Bad url\n";  
        return;  
    }  
  
    boost::urls::url_view url = *purl;  
  
    for (const auto &it : url.params())  
    {  
        std::cerr << "  " << it->key << " -> " << it->value << "\n";  
    }  
}  
  
int main()  
{  
    test_url("/hello?foo=bar");        // Prints foo -> bar  
    test_url("/hello?foo%5b%5d=bar");  // Prints foo[] -> bar  
    test_url("/hello?foo[]=bar");      // Fails (bad url)  
    test_url("/hello?foo=bar%5b%5d");  // Prints foo -> bar[]  
    test_url("/hello?foo=bar[]");      // Fails (bad url)  
  
    return 0;  
}  
```  
  
This behavior may look fine for whatever the spec says, but due a quirk in how firefox handles urls, it makes it near impossible to handle query parameters that include brackets.  
  
To summarize:  
* Have a URL with a bracket in a query param urlencoded  
* Open it in firefox, it will send correct request where those params are urlencoded, but will replace them with unencoded brackets in address bar (`foo%5b%5d` will become `foo[]`)  
* Click on address bar and press enter without modifying the URL, this time firefox will send request where brackets are not urlencoded and boost/url will fail to parse  
  
Chromium on the other hand seem to not change them in address bar so copy/pasting URLs etc. won't cause an issue with boost/url.  
  
I've found [this comment from  8 years ago](https://bugzilla.mozilla.org/show_bug.cgi?id=1121826#c6) in firefox bug tracker that describes exactly what I've seen, so the situation is unlikely to improve on firefox side soon.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-04-23 00:46:45 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1518907334  

By the way you can just write `purl->params()` and do away with the extra local variable.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-04-24 13:30:52 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1520167016  

Yes. The query components are supposed to have unencoded brackets:  
  
```  
   query         = *( pchar / "/" / "?" )  
   unreserved    = ALPHA / DIGIT / "-" / "." / "_" / "~"  
   pchar         = unreserved / pct-encoded / sub-delims / ":" / "@"  
   sub-delims    = "!" / "$" / "&" / "'" / "(" / ")"  
                 / "*" / "+" / "," / ";" / "="  
```  
  
Including an option to conditionally accept these invalid queries seems OK, although I don't know what the API would look like. On the other hand, unconditionally accepting more and more subsets of invalid URLs because platform X or Y accepts them too is problematic because the library is now generating URLs that don't mean or behave the same everywhere. We would now be generating URLs Firefox understands as valid, Chrome understands to mean something else, and other servers will reject as invalid.   
  
Another option is just using the existing API to handle these cases of invalid URL as encoded input where we know what the components should be: 1) `url u(s.substr(0, s.find_first_of('?')))` then 2) `u.set_encoded_query(s.substr(s.find_first_of('?')))`. `set_encoded_X` can't be confused and encodes whatever needs encoding because it knows to what component the content is going. This requires us to think about allocations but so do all transformations.   
  
We could also just avoid problematic URLs. But that depends a lot on the application, of course.

---

## Comment 3

> Username: mserdarsanli  
> Created at: 2023-04-24 17:31:16 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1520566023  

I don't think generating URLs needs changing, as emitting `%5b` instead of `[` is the correct thing to do and both browsers accept such URLs fine. It is just that Firefox breaks the URL for the sake of pretty printing in address bar, and for my site to function properly I need to parse those bad URLs.  
  
> `u.set_encoded_query(s.substr(s.find_first_of('?')))`  
  
Seems like this would work, though I am surprised by the API named `set_encoded_query` that allows unencoded parts and encodes them.  
  
I assume most people are unaware of this issue, and they would want this parsing behavior to be the default as they would want their sites to function. It is also unlikely to be found on development as it requires using Firefox, having a string with brackets to be used in URL, and manually resubmitting the URL.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2023-04-24 19:13:38 UTC  
> Updated at: 2023-04-24 19:13:53 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1520695162  

> I assume most people are unaware of this issue, and they would want this parsing behavior to be the default   
  
"this parsing behavior" is to accept non-conforming inputs. There is very little chance of that happening. For security reasons, and for general sanity, the parsers that I write are quite strictly conforming to the specification. If you want to accept non-conforming inputs, then you need to first detect that they are non-conforming, and second transform the input into something that is conforming according to your preferred logic. Then, call into Boost.URL.  
  
We are open to providing a function to do this for you, e.g.:  
  
```cpp  
/** Apply percent-encoding to brackets in the query of a string, in-place.  
  
    This function applies percent-encoding to any brackets found, starting  
    at the end of the string and working backwards until the first question  
    mark ('?') is encountered. If no question mark is encountered, the  
    function does nothing.  
  
    @note This is a workaround for Firefox non-conformance.  
*/  
void percent_encode_query_brackets( std::string& u );  
```  
  
But you could probably write it yourself...

---

## Comment 5

> Username: mserdarsanli  
> Created at: 2023-04-24 20:59:32 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1520817595  

What I mean by that is not changing default parsing behaviour in boost/url, but this could be more discoverable and easy to do, like an optional parameter to url constructor similar to `encoding_opts` that can be set to opt into this behavior. Because this is something I need to do every time I am constructing a url

---

## Comment 6

> Username: alandefreitas  
> Created at: 2023-04-25 02:04:26 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1521052907  

> that can be set to opt into this behavior  
  
To be clear, there's no need to opt into this behavior because this is the default and intended behavior. In fact, this is the only behavior. Here's the difference between `set_encoded_X` and `set_X`:  
  
- [`set_encoded_X(x)`](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__url/set_encoded_query.html) is aware of the requirements of component `X` and expects `x` to be a valid [percent-encoded string view](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__pct_string_view.html). Any encoded substrings will be passed through to the component unmodified. Any invalid chars for `X` are encoded. Other valid chars remain unencoded.   
- [`set_X(x)`](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__url/set_query.html) is also aware of the requirements of component `X` but expects `x` to be a regular unencoded [string view](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__string_view.html). `x` is not supposed to have percent-encoded octets. Any percent-encoded octets will be treated as literal chars `"%.."` and reencoded in the component (`"%AA"` becomes `"%25AA"`). Any invalid chars for `X` are encoded. Other valid chars remain unencoded.   
  
This intentional behavior ensures errors related to encoding will only happen when constructing the `pct_string_view`, which can be [verified independently](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__make_pct_string_view.html), the function will only throw if there's a much rarer allocation error, and the user doesn't have to remember what chars are valid for `X`. The only thing the user needs to know is if `x` represents a string with or without percent-encoded octets. This is also the default behavior of [`urls::format`](https://www.boost.org/doc/libs/master/libs/url/doc/html/url/ref/boost__urls__format.html).  
  
So if you are producing a `url` from a url string that might contain invalid chars in its query, all you need is to `set_encoded_query` separately. Any invalid `"[]"` will be encoded and any valid `%5B%5D` will pass through to your query.   
  
In fact, if one doesn't care about validation at all, you can "parse" invalid URL components just by looking for the delimiters (`:`, `//`, `?`, `#`, ...) because a delimiter in a complete URL is not allowed to exist in a preceding component, and then let the `set_encoded_X` functions reencode it for you accordingly. This is, of course, just a hypothetical description of what you could do. You shouldn't really do that unless you really need it for performance reasons.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-04-25 10:40:45 UTC  
> Url: https://github.com/boostorg/url/issues/742#issuecomment-1521567544  

> if one doesn't care about validation at all, you can "parse" invalid URL components just by looking for the delimiters (:, //, ?, #, ...) because a delimiter in a complete URL is not allowed to exist in a preceding component, and then let the set_encoded_X functions reencode it for you accordingly.  
  
Now that is interesting :) Maybe we should make an example program that does this?
