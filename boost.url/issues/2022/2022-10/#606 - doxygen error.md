# #606 - doxygen error [Closed]

> Username: vinniefalco  
> Created at: 2022-10-20 20:06:27 UTC  
> Updated at: 2022-10-26 21:17:10 UTC  
> Closed at: 2022-10-26 21:17:10 UTC  
> Url: https://github.com/boostorg/url/issues/606  

This is happening:  
> Error: no ID for constraint linkend: "url.ref.boost__urls__pct_string_view.operator_star_".

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-10-20 23:50:31 UTC  
> Url: https://github.com/boostorg/url/issues/606#issuecomment-1286284434  

Locally I get something like:  
  
```  
xslt-xsltproc bin.v2/libs/url/doc/url_doc.docbook  
xslt-xsltproc-dir libs/url/doc/html/url_HTML.manifest  
Error: no ID for constraint linkend: "url.ref.boost__urls__ipv4_address.ipv6_address".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_base.params_ref".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_base.params_view".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_base.url_view_base".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_base.params_encoded_ref".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_base.params_encoded_view".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_base.url_view_base".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_ref.url_base".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_view.params_encoded_ref".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_view.params_view".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_view.query_rule_t".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_encoded_view.url_view_base".  
Error: no ID for constraint linkend: "url.ref.boost__urls__params_ref.url_base".  
```  
  
but no   
  
```  
Error: no ID for constraint linkend: "url.ref.boost__urls__pct_string_view.operator_star_".  
```  
  
like we get in https://drone.cpp.al/boostorg/url/1697/12/2  
  
I've tried cloning boost again, and redoing everything in a VM. I always get the same output locally.   
  
I have no idea what that is. Probably I'm installing something wrong. But I can't replicate the problem.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2022-10-21 02:14:07 UTC  
> Url: https://github.com/boostorg/url/issues/606#issuecomment-1286368192  

@evanlenz any ideas?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-10-21 17:20:27 UTC  
> Url: https://github.com/boostorg/url/issues/606#issuecomment-1287239199  

You need to figure out why you are getting different results locally. This has nothing to do with docca, it is strictly a Doxygen issue.
