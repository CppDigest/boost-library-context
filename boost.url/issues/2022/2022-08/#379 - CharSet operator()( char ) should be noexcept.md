# #379 - CharSet operator()( char ) should be noexcept [Closed]

> Username: akrzemi1  
> Created at: 2022-08-11 09:39:07 UTC  
> Updated at: 2022-08-12 22:42:07 UTC  
> Closed at: 2022-08-12 22:42:07 UTC  
> Url: https://github.com/boostorg/url/issues/379  

Is the whole abstraction of character sets necessary in the library that deals with urls? Is the set not hardcoded in the rfc? Or will I ever need to use `lut_chars` explicitly when dealing with urls?  
  
And a more specific issue. The specs for function https://master.url.cpp.al/url/ref/boost__urls__validate_pct_encoding/overload1.html say that it throws nothig, but it uses  `CharSet`, and the concept does not require that its models must not throw. Even constexpr does not imply no-throw.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-11 13:57:41 UTC  
> Url: https://github.com/boostorg/url/issues/379#issuecomment-1212026752  

> Is the whole abstraction of character sets necessary in the library that deals with urls? Is the set not hardcoded in the rfc? Or will I ever need to use lut_chars explicitly when dealing with urls?  
  
The design of the library follows a "99/1" rule. Part of the public interface of the library is designed to satisfy 99% of users (`url`, `url_view`, `static_url`) while the rest of the public interface is designed to satisfy the 1%.   
  
Note:  
  
* percent-encoding occurs in non-URL contexts  
* The reserved character set varies depending on the URL part  
* The reserved character set can vary by scheme  
  
As most use-cases will be limited to the well-known schemes (http, https, ftp, ws, wss, file), most users will not see CharSets or need to interact with them. But if you start trying to implement other schemes on top of the library then you will eventually care. And if you want to do percent-encoding outside of URL contexts (such as in HTTP message bodies when using _Content-Type: application/x-www-form-urlencoded_) then you will need to be precise and specify a character set with the percent encoding and decoding free functions.  
  
As the library designer I have made the choice to make the scope of the library a bit more broad that what someone might expect, because I am positioning this library as a foundational component for a family of libraries used to implement various protocols. That is why the algorithms for parsing have been formalized and documented. For example my in-development library Boost.HTTP.Proto uses the interfaces in the `boost::urls::grammar` namespace to parse the HTTP request-line:  
https://github.com/vinniefalco/http_proto/blob/aae0a1055c4102c7241ef9c16a187addddbd7d30/include/boost/http_proto/rfc/detail/rules.hpp#L145  
  
This begs the question, why isn't this proposed in a separate library? Four reasons: it would be too small, too overly specific, too limited, and too much overlap to existing libraries.  
  
The other obvious question is "why not use Spirit" which I am sure will come up. There are many reasons:  
  
* Spirit does too much  
* Taking a dependency on a large Boost library turns away users  
* Keeping parsing in-house (i.e. as a leaf in the dependency graph) has security advantages  
* in-house algorithms are more suited to purpose. They are designed from the ground up with the needs of the library in mind.  
  
> The specs for function https://master.url.cpp.al/url/ref/boost__urls__validate_pct_encoding/overload1.html say that it throws nothig, but it uses CharSet, and the concept does not require that its models must not throw. Even constexpr does not imply no-throw.  
  
I did think that `constexpr` was implicitly `noexcept` (was I wrong?). The _CharSet_ concept should probably require `noexcept` for `operator()( char )`.  
  
But the general answer here, is that if you look closely you will probably find more inconsistencies and documentation bugs. These need to be shaken out. My personal development process is one of rapid iteration and many changes over time, so I tend not to invest too heavily in specifications and documentation up front since those always change. Then towards the end (meaning, a week or two before review) when I know that things are stable is when I start doing many passes through documentation and interfaces to clean stuff up. It is also helpful when people point these things out as I don't always see them.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-08-11 16:24:10 UTC  
> Url: https://github.com/boostorg/url/issues/379#issuecomment-1212210618  

Part of this answer could go into the docs :)

---

## Comment 3

> Username: akrzemi1  
> Created at: 2022-08-11 22:08:10 UTC  
> Url: https://github.com/boostorg/url/issues/379#issuecomment-1212545969  

`constexpr` only requires that there is only one combination of function parameters for which the function can return a compile-time constant. For all other values, it can do runtime things, including throwing exceptions.  
  
`constexpr` requirement cannot be enforced statically via a concept or a type trait. Nor do you need the operation to be constexpr. It is just an encouragement.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-08-11 22:19:31 UTC  
> Url: https://github.com/boostorg/url/issues/379#issuecomment-1212553480  

> or do you need the operation to be constexpr.  
  
You mean on `operator()`? Yeah, there's no requirement or benefit for it being constexpr. But it should be `noexcept`
