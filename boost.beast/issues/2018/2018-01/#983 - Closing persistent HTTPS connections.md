# #983 - Closing persistent HTTPS connections [Closed]

> Username: sdc395  
> Created at: 2018-01-15 14:44:24 UTC  
> Updated at: 2018-01-16 16:58:53 UTC  
> Closed at: 2018-01-16 16:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/983  

Hi  
  
I'm encountering an issue during client-side SSL shutdown when the Beast-based HTTP server is waiting on an asynchronous read operation.  I was expecting the client-side SSL shutdown call to abort the server-side read operation, allowing the server to conduct its half of the SSL shutdown procedure.  Instead, the server continues to wait on the read operation.  The synchronous client-side call to asio::ssl::stream::shutdown never returns.  
  
Client and server are both using Boost 1.66.  Client has its own HTTP client implementation on top of asio::ssl::stream, server code is based on Beast's async HTTPS server example.  
  
Is this behaviour expected?  If so, how should I close a persistent HTTPS connection when I have no more requests to send?  
  
Thanks

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-15 16:50:49 UTC  
> Url: https://github.com/boostorg/beast/issues/983#issuecomment-357736836  

>I was expecting the client-side SSL shutdown call to abort the server-side read operation, allowing the server to conduct its half of the SSL shutdown procedure.  
  
That is correct.  
  
>Instead, the server continues to wait on the read operation.  
  
Can you reproduce this behavior using the unmodified examples **http-client-sync-ssl** and **http-server-async-ssl**?

---

## Comment 2

> Username: sdc395  
> Created at: 2018-01-16 15:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/983#issuecomment-357996642  

Hi Vinnie  
  
Thanks for the response and for confirming the expected behaviour.  Turns out this was my own mistake (too embarrassed to explain) so this ticket can be closed.  Sorry to have wasted your time.  
  
Thanks for all your work on Beast.  It's great!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-16 16:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/983#issuecomment-358029787  

\phew, glad to hear it! This would have been quite difficult for me to track down! No time wasted, feel free to open any new issues as needed.
