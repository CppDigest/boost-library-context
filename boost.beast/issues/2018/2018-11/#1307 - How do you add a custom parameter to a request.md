# #1307 - How do you add a custom parameter to a request? [Closed]

> Username: JanVerschaeren  
> Created at: 2018-11-19 12:45:02 UTC  
> Updated at: 2020-01-28 13:00:43 UTC  
> Closed at: 2018-11-19 13:34:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1307  

Hi,  
  
How do you add a parameter to a request?  
I am using java (spring) for the server side. And this server requests parameter named "path".  
I created a request called req then i tried this: `req.insert("path", "path/to/file");`.  
But the server still complains that it doesn't find a parameter called "path".

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-19 13:26:33 UTC  
> Updated at: 2018-11-19 13:27:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-439891940  

Are you talking about URI parameters? Or are you talking about HTTP fields? URI parameters go at the end of the target string, for example `GET /?path=path/to/file HTTP/1.1` you will need to handle your own URL encoding for this as well.

---

## Comment 2

> Username: JanVerschaeren  
> Created at: 2018-11-19 13:34:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-439894248  

I needed an URI parameter apparently, thanks for the quick response! :)

---

## Comment 3

> Username: pravic  
> Created at: 2019-03-26 14:26:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-476670893  

> URI parameters go at the end of the target string  
  
I wish there was a more convenient way to do this (`boost::uri`? `boost::beast::uri`?).

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-03-26 14:29:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-476672298  

> I wish there was a more convenient way to do this (boost::uri? boost::beast::uri?).  
  
Me too! I will get around to adding `beast::uri` eventually...

---

## Comment 5

> Username: neel  
> Created at: 2020-01-28 11:25:06 UTC  
> Updated at: 2020-01-28 11:26:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-579200663  

Any updates on this issue ?  
I am trying to extract uri and the get query parameters from the request.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-01-28 13:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1307#issuecomment-579234189  

Hi @neel Vinnie is working on the boost.url library as we speak. It will be released as a separate library rather than part of boost.beast.  
  
In the meantime, there are a number of uri-parsing libraries out there.  
  
One I have used in the past is the c library uriparser:   
  
https://github.com/uriparser/uriparser  
  
You can track the progress of boost.url here:  
  
https://github.com/vinniefalco/url
