# #123 - Differentiate consumers and producers [Closed]

> Username: alandefreitas  
> Created at: 2022-02-14 23:53:02 UTC  
> Updated at: 2022-03-08 14:08:26 UTC  
> Closed at: 2022-03-08 04:32:13 UTC  
> Url: https://github.com/boostorg/url/issues/123  

This is a generalization of and derived from #93. We need to reach an agreement before fixing that.   
  
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

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-15 16:51:56 UTC  
> Url: https://github.com/boostorg/url/issues/123#issuecomment-1040517724  

The documentation should also be specific on the separate individual responsibilities of producers and consumers.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-02-22 06:21:30 UTC  
> Url: https://github.com/boostorg/url/issues/123#issuecomment-1047464067  

Is this a documentation-only issue? Or is it actionable in terms of changing code?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-02-23 17:35:56 UTC  
> Url: https://github.com/boostorg/url/issues/123#issuecomment-1049035574  

> Is this a documentation-only issue?   
  
No.  
  
> Or is it actionable in terms of changing code?  
  
Yes. The consumers would be looser than producers.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-03-08 05:07:13 UTC  
> Url: https://github.com/boostorg/url/issues/123#issuecomment-1061410976  

> Yes. The consumers would be looser than producers.  
  
What code should change?

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-03-08 14:08:26 UTC  
> Url: https://github.com/boostorg/url/issues/123#issuecomment-1061816558  

> What code should change?  
  
From the library design and our multiple sparse previous discussions about this issue, I think there's no code that can or should change. There's also nothing to document because there's no differentiation in the grammar between producers and consumers.
