# #187 - Docs: provide rationale for the name URL [Closed]

> Username: akrzemi1  
> Created at: 2022-06-06 07:12:23 UTC  
> Updated at: 2022-06-10 22:16:22 UTC  
> Closed at: 2022-06-10 22:16:22 UTC  
> Url: https://github.com/boostorg/url/issues/187  

It looks like according to rfc3986 what the library does is parsing, transforming, and communicating *URI*s (Universal Resource **Identifiers**). It is my understanding that name "URL" is chosen instead because:  
  
1. URL is a name far more recognized in the industry.  
2. It is better for marketing purposes.  
3. The anticipated usage of the library is to process URLs with http and https protocols.  
  
This should be clearly stated in the docs, along with saying that technically, these are URIs. I would expect a clearer distinction that rfc3986 is followed only in terms of grammar, but not necessarily in terms of names.  
  
Additional explanation is required for function `parse_uri`: it uses URI in the name but returns an `url`, which looks inconsistent. Inconsistency is acceptable when a sufficient rationale in the docs is provided.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-08 22:07:02 UTC  
> Url: https://github.com/boostorg/url/issues/187#issuecomment-1150463286  

All the parse functions which return `result<url_view>` could have a sentence along the lines of  
  
"The `url_view` container can refer to any valid URI. It is named after URL because the other terms (URI and URN) never achieved sufficient recognition."
