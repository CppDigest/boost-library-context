# #93 - Handle bracket characters in query key parameters [Closed]

> Username: AlexandreBossard  
> Created at: 2021-12-29 15:51:50 UTC  
> Updated at: 2023-12-15 18:42:29 UTC  
> Closed at: 2023-12-15 18:42:29 UTC  
> Url: https://github.com/boostorg/url/issues/93  

version used beta-1  
e.g.: `https://hello.word?keys[]=value1&keys[]=value2`  
  
I know this is not part of the the RFC, but it has unfortunately enough widespread use to let boost-url handle it.  
all `parse_*` function reject a non encoded brackets in a query key.  
passing a `key[]`  to `url.params().emplace_back()` for example, will encode the brackets auto-magically. This is, I believe the correct behavior, but it's not "historically` accurate and may not works for everyone (I have no proof of that claim!)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-29 16:06:10 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1002666126  

related:  
https://stackoverflow.com/questions/11490326/is-array-syntax-using-square-brackets-in-url-query-strings-valid

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-12-29 16:22:25 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1002673167  

This additional signature could work:  
```  
bool parse_uri( url& dest, string_view s, error_code& ec, parse_options const& opt = {} );  
```  
  
It would require a separate bnf to handle non-compliant queries.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-14 21:32:14 UTC  
> Updated at: 2022-02-14 21:45:03 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1039585039  

We need to reach an agreement before fixing that.   
  
My proposed solution is we differentiate between producers `url` and consumers `url_view` as defined by the RFC. `url` would always encode most `gen-delims` but `url_view` would accept unencoded `gen-delims` that are not ambiguous without any "loose" parsing mode.   
  
I have two reasons and some evidence for each:  
  
1) The reserved characters change depending on the URL component. Even for producers (`url`), the RFC allows more than the reserved characters in some subcomponents.  
    1) The general case forbids `gen-delims`: Of the ASCII character set, the characters `: / ? # [ ] @` (gen-delims) are reserved for use as delimiters of the generic URI components and must be percent-encoded – for example, `%3F` for a question mark. [RFC3986 2.2](https://www.rfc-editor.org/rfc/rfc3986.html#section-2.2)  
    2) The general case allows `sub-delims` that are not ambiguous:  
        1) The characters `! $ & ' ( ) * + , ; =` are permitted by generic URI syntax to be used unencoded in the user information, host, and path as delimiters. [RFC3986 3.2.2](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.2.2) and [RFC3986 3.3](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.3)  
        2) Additionally, `:` and `@` may appear unencoded within the path, query, and fragment; and `?` and `/` may appear unencoded as data within the query or fragment. [RFC3986 3.3](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.3), [RFC3986 3.4](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.4), and [RFC3986 3.5](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.5)  
2) Consumers should accept reserved characters that are not ambiguous. For producers (`url`), the RFC tells us to usually encode the reserved characters `gen-delims`, but it also says very often consumers (`url_view`) should accept reserved characters that not ambiguous in that component.   
    1) [RFC3986](https://www.rfc-editor.org/rfc/rfc3986.html) and [RFC2396](https://datatracker.ietf.org/doc/html/rfc2396) define a difference between producers and consumers, even though they talk much more about producers and these references are sparse.  
    2) Producers should use unencoded chars sometimes  
        1) Even for producers, it is sometimes recommended for usability to avoid percent-encoding some reserved characters in `sub-delims`. [RFC3986 3.4](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.4)   
    2) Consumers should accept unencoded chars that are not ambiguous. There's no need for a "loose" parsing mode.   
        1) The regular expression ` ^(([^:/?#]+):)?(//([^/?#]*))?([^?#]*)(\?([^#]*))?(#(.*))?` is also considered a valid URL for consumers. This includes reserved delimiters that are not ambiguous. [RFC3986 B](https://www.rfc-editor.org/rfc/rfc3986.html#appendix-B)  
        1) Everything between the first `?` and the first `#` fits the spec's definition of a query.  It can include any characters such as `: / . ?`. [RFC3986 3.4](https://www.rfc-editor.org/rfc/rfc3986.html#section-3.4) and [SO question](https://stackoverflow.com/questions/4557387/is-a-url-query-parameter-valid-if-it-has-no-value/41689431#41689431)  
        2) [HTML](http://www.w3.org/TR/html401/interact/forms.html#form-content-type) establishes that a form submitted via HTTP GET should encode the form values as name-value pairs in the form "`?key1=value1&key2=value2...`" (properly encoded). Parsing of the query string is up to the server-side code (e.g. Java servlet engine). URIs should support that.  
        3) Accepting reserved chars that are not ambiguous is common practice for consumers: I replicated the consumer algorithm used by Apache [here](https://godbolt.org/z/5eKKcEq7d). It basically accepts anything that is not ambiguous. For instance, anything after `#` is a valid fragment. For instance, anything but `#` after `?` is a valid query, and so on. All other libraries I checked, including Apache, Javascript URL and folly, present the same behaviour.  
        4) I don't know what was on their mind when allowing consumers to accept non-ambiguous delimiters, but this relaxation allows parsers to be faster. For instance, the Apache algorithm just looks for the delimiters `?` and then `#`, and something similar happens for other components.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-02-14 22:09:46 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1039620138  

I think we should focus on the query instead of broadening the question to the entire URL

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-02-14 22:46:46 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1039649191  

> I think we should focus on the query instead of broadening the question to the entire URL  
  
We could just change the query BNF to accept `[` and `]` and fix this. It's just that related issues keep coming up all the time and they're probably not going to stop.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-02-16 19:42:06 UTC  
> Updated at: 2022-02-16 19:43:54 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1042096083  

I think that the grammar for query should accept any unescaped character except the pound sign ( `#` ), and that any percent sign ( `%` ) must be followed by two valid hex digits. When converting an unencoded string into a percent-encoded query string, it should use the general character specified in the RFC:  
```  
    query           = *( pchar / "/" / "?" )  
```

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-02-16 22:10:46 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1042359872  

> I think that the grammar for query should accept any unescaped character except the pound sign ( # )  
  
This is what #124 ended up doing. The only reserved chars it didn't accept before were `['#', '[', ']']`.  
  
> any percent sign ( % ) must be followed by two valid hex digits  
  
We should probably expand that to other components

---

## Comment 8

> Username: vinniefalco  
> Created at: 2022-02-18 20:07:07 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1045114757  

> We should probably expand that to other components  
  
I think it already works that way, right ?

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-02-18 20:51:38 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1045168983  

> I think it already works that way, right ?  
  
`key_chars` accepts whatever is in `unreserved_chars  + subdelim_chars + ':' + '@' + + '/' + '?'- '&' - '='`. You mean we should wrap it `pct_encoded_rule` like `pct_encoded_rule<fragment_chars_t>`, right?

---

## Comment 10

> Username: alandefreitas  
> Created at: 2022-02-18 21:13:34 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1045199207  

If a query doesn't need to be interpreted as params, why do we parse a range of key/value pairs in `query_rule`? If we remove this constraint, we could parse a `query_rule` as `pct_encoded_rule<query_chars_t>` and just make it:  
  
```  
constexpr  
    query_chars_t() noexcept  
        : grammar::lut_chars(  
            pchars + '/' + '?' + '[' + ']')  
    {  
    }  
```  
  
or something directly more permissive like  
  
```  
constexpr  
    query_chars_t() noexcept  
        : grammar::lut_chars(  
            pchars + gen_delim_chars - '#')  
    {  
    }  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2022-02-19 05:39:29 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1045818446  

Because we need to know how many key/value pairs there are

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-02-19 15:09:57 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1046039638  

> Because we need to know how many key/value pairs there are  
  
OK. As `key_chars` and `value_chars` are already wrapped in `pct_encoded_rule`, I believe PR #124 is ready then.  
  
```  
pct_encoded_rule<  
        query_rule::key_chars> t0;  
    pct_encoded_rule<  
        query_rule::value_chars> t1;  
```

---

## Comment 13

> Username: mkarasevych  
> Created at: 2023-12-14 23:10:41 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1856909813  

Hi! Although this issue is closed, I'm encountering a similar problem. Could you clarify whether square brackets are accepted as part of a query? My test fails in boost 1.84:  
```  
    auto origin1 = "/path/path?key=value";  
    auto rv1 = boost::urls::parse_origin_form( origin1 );  
    BOOST_CHECK( rv1.has_value() );  
  
    auto origin2 = "/path/path?key[]=value";  
    auto rv2 = boost::urls::parse_origin_form( origin2 );  
    BOOST_CHECK( rv2.has_value() ); // fails  
    BOOST_CHECK( rv2.error().message() == "leftover" ); // passes  
```

---

## Comment 14

> Username: alandefreitas  
> Created at: 2023-12-15 14:54:37 UTC  
> Url: https://github.com/boostorg/url/issues/93#issuecomment-1858010035  

@mkarasevych Thanks for reporting that.   
  
Yes. It seems like #124 wasn't enough. I'll have a look.
