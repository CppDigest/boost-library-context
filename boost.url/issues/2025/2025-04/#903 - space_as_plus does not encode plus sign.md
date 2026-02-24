# #903 - space_as_plus does not encode plus sign [Closed]

> Username: yangfl  
> Created at: 2025-04-11 01:59:29 UTC  
> Updated at: 2025-05-15 21:04:08 UTC  
> Closed at: 2025-05-15 21:04:08 UTC  
> Url: https://github.com/boostorg/url/issues/903  

EDIT: for anyone seeking a quick fix: `boost::urls::encode("aA1 +?", boost::urls::unreserved_chars + "whatever your unreserved chars")`  
  
With the default option `encoding_opts::space_as_plus = true`, `url::params()` decodes plus sign into space, but never encodes it into `%2B`, leading to surprising `(*url.params().find(key)).value != value`.  
  
https://compiler-explorer.com/z/n15TqsocE  
  
```cpp  
#include <iostream>  
#include <boost/url.hpp>  
namespace urls = boost::urls;  
  
int main() {  
    {  
        urls::url url("https://a/a");  
  
        auto params = url.params();  
        params.append({"k", "500+500"});  
  
        for (const auto &param : url.params()) {  
            std::cout << param.key << " " << param.value << std::endl;  
        }  
    }  
}  
```  
  
Output: `k 500 500`

---

## Comment 1

> Username: alandefreitas  
> Created at: 2025-04-11 16:03:20 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2797345682  

This looks correct to me. `params()` uses default encoding opts, `append` assumes it's receiving input to be encoded, and then it encodes `+` as whitespace, as defined in the default encoding opts.  
  
Aren't you expecting [`params`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/url_base/params-0b1.html)/[`params_ref`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/params_ref.html)/[`params_view`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/params_view.html) to behave like [`encoded_params`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/url_base/encoded_params-07.html)/[`params_encoded_ref`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/params_encoded_ref.html)/[`params_encoded_view`](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/params_encoded_view.html)? You probably want `encoded_params()` so the behavior is not surprising/encoded.  
  
Although I agree, it's surprising that an element of `params` looks different when appending and retrieving. I'm not sure it's possible to make them always look the same because these URL transformations are not one-to-one or reversible.

---

## Comment 2

> Username: yangfl  
> Created at: 2025-04-12 14:10:18 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2798846388  

It does not encode `+` into whitespace; actually it encodes into `%20`.  
  
```cpp  
url.params().append({"k", "a+b c"});  
std::cout << url << std::endl;  
// https://a/a?k=a+b%20c  
```  
  
The problem is, `params()` decodes `+` into whitespace, which is desirable, but somehow leaves `+` unencoded. If encoded `+` means decoded whitespace, shouldn't decoded `+` be mapped to something else?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2025-04-17 18:31:01 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2813737057  

Yes. That makes sense. I'll have a look at this.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2025-05-08 22:48:20 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2864608529  

I've been experimenting with this for a while now. The root of the problem is that this library is based on RFC 3986, but also supports this extra encoding option that's not only not part of the RFC (spaces as plus is an HTTP thing) but ambiguous with its rules. Although the problem is easy to explain ("what represents a literal plus when plus means space?"), it is quite complex to solve for two reasons:  
  
## 1) The normalization problem  
  
The intuition for an initial solution is simple: if `+` and ` ` represent whitespaces (it's even quite common for applications to directly encode "+" as "%20" when it's in the query), then something has to represent a literal `+` and the obvious choice is `%2B`. However, when we normalize a URL, its meaning shouldn't change, and normalizing "%2B" in a query gives us "+" because "+" is an unreserved query char and all unreserved chars should be decoded during normalization. But now "%2B" became "+", changing its meaning from "+" to " ".   
  
For this problem, the obvious intuition would be to just implement an extension to RFC 3986 where normalization would take this new rule into account and not decode "%2B" into "+" when we're encoding space as plus. However, this creates a design problem, because URLs or URL views in the library don't carry information about which encoding options are used by each component. When calling a function such as `params()`, the library assumes encoding options are just options regarding how the query parameters should be interpreted locally. The library doesn't assume these encoding options are a property of the URL.  
  
- One solution would be to also extent other functions, such as `url_base::normalize()` to also accept encoding options and then the user would be responsible for ensuring the same encoding options are being used whenever they call a functions that accepts encoding options. This is unsafe and semantically wrong.  
  
- A second solution would be to give URLs this property so each URL knows which encoding options it assumes. This opens up a new set of design problems. How would every other function that parses and creates URL need to change? How are URLs with different encoding options compared? Should URLs be enforcing rules that are specific to HTTP URLs? Which encoding options should be the default (the default for `params()` is  `space_as_plus` as `true`, which is specific to HTTP)? What happens to existing functions that currently accept encoding options? What happens to existing functions that currently do to accept encoding options? This would be breaking change almost for sure.  
  
- A third solution would be a breaking change where `params()` would have `space_as_plus = false` by default. That means URLs don't use HTTP specific rules that cause ambiguity by default. The normalization problem still remains if the user explicitly decides to interpret the URL in a way that goes beyond the RFC 3986 rules.  
  
- A fourth option is to only change this behavior for `params_ref` when you call `append` because now we're talking about query parameters. So appending "500+500" would append an encoded "500%2B500" and this would be decoded as "500+500" instead of "500 500". In this case, the user would be responsible for not normalizing the query string because normalization would use RFC 3986 rules that would make this ambiguous. This is not so simple to implement because `append` and many other functions currently use a private URL function `edit_params` that interacts with other functionality but the behavior is easy to describe. The problem here is we're leaving many problems unsolved and might even be making behavior more complex.  
  
## 2) The standardization problem  
  
Although this might be a smaller problem, whatever behavior we implement needs to be formalized because we don't want to follow our intuition to later find out some other standard actually describes how this should work. As I understand it, this "space-as-plus rule" is mentioned sparsely in the [WHATWG standard](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html), which we don't track with this library because it's a "live" standard.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2025-05-09 14:49:36 UTC  
> Updated at: 2025-05-09 17:28:48 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2866846626  

Here's the spec: https://datatracker.ietf.org/doc/html/rfc1866#section-8.2.1 / https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4.1  
  
Unfortunately still ambiguous:  
  
> “space characters are replaced by `+’, and then reserved characters are escaped as per [URL];“,   
  
but "URL" says we _must not_ encode “+”, so “+” could represent both ” ” and “+”.  
  
My partial conclusion is `encoding_opts` in Boost.URL shouldn’t exist at all. Now that it exists, we need a breaking change:  
  
- The default `encoding_opts` should always be the one that follows the RFC 1866 rules (no space-as-plus by default).  
- `params` would decode `+` as ` ` when space-as-plus was explicitly asked for  
- `params` would encode `+` as `%2B` when space-as-plus was explicitly asked for  
- These would only affect params locally and attempting to normalize a URL that encodes `+` as `%2B` would make it ambiguous.

---

## Comment 6

> Username: yangfl  
> Created at: 2025-05-09 15:06:48 UTC  
> Url: https://github.com/boostorg/url/issues/903#issuecomment-2866905758  

Agree. But maybe [space_as_plus](https://www.boost.org/doc/libs/master/doc/antora/url/reference/boost/urls/encoding_opts/space_as_plus.html)  
  
> True if spaces encode to and from plus signs  
  
should be clarified as "spaces encode from plus signs only" for now.
