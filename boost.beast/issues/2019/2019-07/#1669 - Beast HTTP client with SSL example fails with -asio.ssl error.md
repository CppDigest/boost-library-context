# #1669 - Beast HTTP client with SSL example fails with "asio.ssl error" [Closed]

> Username: blastrock  
> Created at: 2019-07-29 14:17:51 UTC  
> Updated at: 2019-09-05 12:24:57 UTC  
> Closed at: 2019-09-05 12:24:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1669  

### Version of Beast  
  
Beast version 181  
Boost 1.68  
  
### Steps necessary to reproduce the problem  
  
I want to start integrating beast in our project, I thought a good starting point would be to copy paste the example from your repo and continue from there. I am not using the last boost version, so I took this example from boost 1.68: https://raw.githubusercontent.com/boostorg/beast/1da229a27c6f0539d422bcedbcf47cfe2517164a/example/http/client/async-ssl/http_client_async_ssl.cpp  
  
I compiled and ran it, and got this error:  
  
    handshake: asio.ssl error  
  
### Resolution  
  
I first learned that you need to add `::SSL_load_error_strings()` at the beginning of your program to get (a bit more) meaningful errors. This gives us the following error:  
  
   handshake: sslv3 alert handshake failure  
  
Which didn't help at all. I didn't manage to get any better error message. In the end, I tried adding `::SSL_library_init()` before creating the ssl context without any conviction and the issue was indeed solved!  
  
I didn't try to compile the example from the boost worktree so I don't know if there are any library or compiler flag that take care of this initialization there, but I think this line should appear in the example as this is an error that is very difficult to track. Maybe the `::SSL_load_error_strings()` call should be there too.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-29 14:29:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1669#issuecomment-516015624  

Now this is very interesting! But unfortunately, it is strictly a Boost.Asio issue, since Asio provides the SSL wrapper implementation. Perhaps @chriskohlhoff can look into it? I suggest you open the issue there: https://github.com/boostorg/asio

---

## Comment 2

> Username: djarek  
> Created at: 2019-07-29 16:18:50 UTC  
> Updated at: 2019-07-29 16:19:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1669#issuecomment-516059854  

https://wiki.openssl.org/index.php/Library_Initialization  
  
> If you are using OpenSSL 1.0.2 or below, then you would use SSL_library_init. If you are using OpenSSL 1.1.0 or above, then the library will initialize itself automatically.  
  
Note: anything below 1.0.2 is considered insecure at this point.

---

## Comment 3

> Username: blastrock  
> Created at: 2019-07-30 09:47:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1669#issuecomment-516348579  

Thanks for the hint! Actually we don't use OpenSSL but LIbreSSL. I think the issue comes from there. If this is not a supported case by beast or asio, I guess we can just close this issue.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-08-29 11:46:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1669#issuecomment-526149583  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-09-05 12:24:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1669#issuecomment-528338890  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
