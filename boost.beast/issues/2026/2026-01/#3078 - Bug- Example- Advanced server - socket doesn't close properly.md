# #3078 - Bug: Example: Advanced server - socket doesn't close properly [Closed]

> Username: pinballcutie1992  
> Created at: 2026-01-27 10:59:03 UTC  
> Updated at: 2026-01-27 18:08:53 UTC  
> Closed at: 2026-01-27 18:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/3078  

Browser successfully receives page, but server wrongly reports "read: The socket was closed due to a timeout". Sometimes multiple times (2 times in my case). Probably something wrong with `http::async_read`, because it always calls `on_read` with `Success` and never with `error::end_of_stream`. Also on last read parser has some data " HTTP/1.1", but I don't understand where it comes from. Tested on linux with boost 1.83 and 1.90 with gcc and clang. Also tested with different browsers - Mozilla Firefox 140.7.0esr and Chromium 144.0.7559.96 built on Debian GNU/Linux 13 (trixie).

---

## Comment 1

> Username: ashtum  
> Created at: 2026-01-27 17:32:39 UTC  
> Url: https://github.com/boostorg/beast/issues/3078#issuecomment-3806528667  

Because HTTP/1.1 does not support multiplexing, Chrome (and most browsers) opens multiple TCP connections when loading a web page to fetch the initial HTML and other required resources (such as images and JavaScript files) in parallel, improving performance.  
  
The timeout you see occurs because the browser keeps the connection alive to reduce latency for subsequent requests. For example, if you refresh the page, you will often see that no new connection is created. In most cases, you can safely ignore timeout messages in your implementation.

---

## Comment 2

> Username: pinballcutie1992  
> Created at: 2026-01-27 18:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/3078#issuecomment-3806711949  

Thank you for detailed explanation
