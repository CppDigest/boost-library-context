# #813 - Slash in query param not being encoded as %2F [Closed]

> Username: msherman13  
> Created at: 2024-01-08 20:19:27 UTC  
> Updated at: 2024-01-09 00:21:03 UTC  
> Closed at: 2024-01-08 20:43:38 UTC  
> Url: https://github.com/boostorg/url/issues/813  

Sorry if I am way off on this as far as the http spec goes but query params containing the "/" character are not being encoded as expected. In the below example, I would expect the output to be: `/?key=value1%2Fvalue2` however the output is actually `/?key=value1/value2`  
  
```  
#include <boost/url.hpp>  
#include <iostream>  
  
int main() {  
	boost::urls::url url("/");  
	url.params().append({"key", "value1/value2"});  
	std::cout << url << std::endl;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-01-08 20:42:08 UTC  
> Updated at: 2024-01-08 20:45:00 UTC  
> Url: https://github.com/boostorg/url/issues/813#issuecomment-1881789720  

What makes you think these need to be escaped? They are not ambiguous, as they are not part of the target. See rfc3986:  
  
https://datatracker.ietf.org/doc/html/rfc3986#section-3.4  
  
```  
      query       = *( pchar / "/" / "?" )  
  
   The characters slash ("/") and question mark ("?") may represent data  
   within the query component.  
```  
  
The RFC even explicitly states that it is better not to escape them:  
```  
                                However, as query components  
   are often used to carry identifying information in the form of  
   "key=value" pairs and one frequently used value is a reference to  
   another URI, it is sometimes better for usability to avoid percent-  
   encoding those characters.  
```

---

## Comment 2

> Username: alandefreitas  
> Created at: 2024-01-08 22:24:53 UTC  
> Url: https://github.com/boostorg/url/issues/813#issuecomment-1881916997  

To complement the answer, the only possible component after the query is a fragment. So the only delimiter after the query is a `#`. That's why `?` and `/` are allowed in the query.

---

## Comment 3

> Username: msherman13  
> Created at: 2024-01-08 22:31:55 UTC  
> Url: https://github.com/boostorg/url/issues/813#issuecomment-1881924932  

@vinniefalco ok makes sense. I was going off this link:  
  
https://en.m.wikipedia.org/wiki/Percent-encoding  
  
additionally, the AWS S3 rest API requires it:  
  
https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-01-08 23:38:42 UTC  
> Updated at: 2024-01-08 23:38:58 UTC  
> Url: https://github.com/boostorg/url/issues/813#issuecomment-1881989572  

Yes. Unreserved Characters can be used in any component, although individual components can allow more than the unreserved set. For instance:  
  
```js  
$> node  
new URL('http://www.example.com/a/s/b?f=g/s')  
URL {  
  href: 'http://www.example.com/a/s/b?f=g/s',  
  origin: 'http://www.example.com',  
  protocol: 'http:',  
  username: '',  
  password: '',  
  host: 'www.example.com',  
  hostname: 'www.example.com',  
  port: '',  
  pathname: '/a/s/b',  
  search: '?f=g/s', // <--------  
  searchParams: URLSearchParams { 'f' => 'g/s' },  
  hash: ''  
}  
```  
  
Whoever wrote the AWS tutorial got that wrong if it's meant as a requirement. I'd be surprised if the server rejects a request because a "/" in the query is unencoded.

---

## Comment 5

> Username: msherman13  
> Created at: 2024-01-09 00:21:01 UTC  
> Url: https://github.com/boostorg/url/issues/813#issuecomment-1882036604  

@vinniefalco its not that the AWS server rejects explicitly if slashes aren’t escaped but the issue is that the authorization signature is calculated based on what they call uri-encoded query params. So if we don’t escape the slash when calculating the signature, their signature won’t match and then they reject.  
  
Anyway, topic for another forum. Thanks for the help
