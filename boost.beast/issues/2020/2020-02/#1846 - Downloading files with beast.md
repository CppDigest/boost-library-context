# #1846 - Downloading files with beast [Closed]

> Username: Xeverous  
> Created at: 2020-02-11 18:54:29 UTC  
> Updated at: 2020-02-11 20:53:57 UTC  
> Closed at: 2020-02-11 20:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1846  

I'm not really knowledgeable on HTTP and FTP so asking this before I accidentally get into implementing something huge or trying to use something Beast does not support.  
  
I would like to download files using Beast, specifically github project releases. GitHub has a JSON REST API and I can query it without problems, but when I attempt to download a file from a link like `https://github.com/user/repo/releases/file.zip` I get such response:  
  
```  
HTTP/1.1 302 Found  
Date: Tue, 11 Feb 2020 17:54:19 GMT  
Content-Type: text/html; charset=utf-8  
Transfer-Encoding: chunked  
Server: GitHub.com  
Status: 302 Found  
Vary: X-PJAX  
Vary: Accept-Encoding, Accept  
Location: [...long hashed link...]  
Cache-Control: no-cache  
Set-Cookie: logged_in=no; domain=.github.com; path=/; expires=Thu, 11 Feb 2021 17:54:19 -0000; secure; HttpOnly  
Set-Cookie: [...long hash...]  
Strict-Transport-Security: max-age=31536000; includeSubdomains; preload  
X-Frame-Options: deny  
X-Content-Type-Options: nosniff  
X-XSS-Protection: 1; mode=block  
Expect-CT: max-age=2592000, report-uri="https://api.github.com/_private/browser/errors"  
Content-Security-Policy: [...long list of sites...]  
X-GitHub-Request-Id: C20B:40468:29D530:3BCC82:5E42EA4B  
  
260  
<html><body>You are being redirected [...]  
0  
```  
  
The link in `Location` is very long but looks like `https://github-production-release-asset-2e65be.s3.amazonaws.com/164753775/[...]%3B%20filename%3Dsomething.zip&response-content-type=application%2Foctet-stream`  
  
Should I just grab the location link by `res[http::field::location]` and issue another GET request with this link used as the host+target? If I get the `octet-stream` right the next response content will just contain the binary file data?  
  
AFAIK Beast does not have any functionality to automate things such as redirects. Also, should I be worried about any other fields I receive or are they purely informational?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-11 18:58:39 UTC  
> Updated at: 2020-02-11 18:59:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1846#issuecomment-584796193  

Hi @Xeverous ,  
  
To cut a long story short, your instincts are correct.  
  
The real clue here is the 3xx-series status code coupled with the presence of a `Location` header.  
  
This indicates that you must follow the redirect as you suggested, but issuing a new request.  
  
Higher level libraries have options to handle this for you, but Boost.Beast is a low level library dealing in the HTTP and Websocket protocols rather than high level session management.

---

## Comment 2

> Username: Xeverous  
> Created at: 2020-02-11 19:06:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1846#issuecomment-584799834  

I don't really care about the details of the connection, more about the content and automatic support of required extras (eg SSL, authentication, redirects). So should I use something like libcurl (or its wrappers) for my needs?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-11 20:42:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1846#issuecomment-584841586  

Beast is a low-level HTTP protocol library that sits just above the portable socket abstraction (Boost.Asio). It will never support handling redirects or anything beyond the minimum number of HTTP fields such as Content-Length, Transfer-Encoding, and Connection. I do have plans to build higher level libraries with Beast, but they are not going to appear soon so you will either need to implement it yourself, or use another library like curl.

---

## Comment 4

> Username: Xeverous  
> Created at: 2020-02-11 20:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1846#issuecomment-584846178  

Ok, thanks for the fast reply.
