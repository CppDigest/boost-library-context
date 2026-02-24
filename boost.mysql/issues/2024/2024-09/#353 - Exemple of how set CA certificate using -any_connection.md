# #353 - Exemple of how set CA certificate using "any_connection". [Closed]

> Username: Malkverbena  
> Created at: 2024-09-25 19:12:36 UTC  
> Updated at: 2024-09-26 05:39:03 UTC  
> Closed at: 2024-09-26 05:39:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/353  

Hi!  
It's not clear to me how to set a CA certificate using "any_connection". Is there any exemple?

---

## Comment 1

> Username: anarthal  
> Created at: 2024-09-25 19:50:55 UTC  
> Url: https://github.com/boostorg/mysql/issues/353#issuecomment-2375121632  

Hi!  
  
Apologies, there is none at the moment - but they're planned. The steps are very similar to what you'd do with regular `connection`. You need to create an `asio::ssl::context`, populate it with the CA information, then pass it to `any_connection` constructor wrapped in `any_connection_params`. It would be similar to [this](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/any_connection.html#mysql.any_connection.tls).  
  
If I'm not mistaken, you need to call `add_certificate_authority` (see [docs](https://live.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/ssl__context/add_certificate_authority.html)) on the ssl context to add the CA.  
  
Let me know if that solves your question.  
  
Thanks,  
Ruben.

---

## Comment 2

> Username: Malkverbena  
> Created at: 2024-09-25 21:48:33 UTC  
> Url: https://github.com/boostorg/mysql/issues/353#issuecomment-2375323460  

Works fine! Thanks.  
And thank you for your work. This client saved me a lot of headaches.

---

## Comment 3

> Username: anarthal  
> Created at: 2024-09-26 05:39:02 UTC  
> Url: https://github.com/boostorg/mysql/issues/353#issuecomment-2375982340  

That's great! Feel free to ask any other questions you may have.
