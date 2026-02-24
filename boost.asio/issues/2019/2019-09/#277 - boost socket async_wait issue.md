# #277 - boost socket async_wait issue [Closed]

> Username: wangjiangw  
> Created at: 2019-09-04 08:11:20 UTC  
> Updated at: 2020-12-30 01:08:43 UTC  
> Closed at: 2020-12-30 01:08:42 UTC  
> Url: https://github.com/boostorg/asio/issues/277  

hi, all  
  
i have an confusing question today，in my code，i had register a handler on socket by using async_wait，code like below  
  
ssl_socket_->next_layer().async_wait(ip::tcp::socket::wait_error, handler);  
  
however, when the handler called later, the error_code showed is 0，and the error message is success, can anyone help me or tell me why ?

---

## Comment 1

> Username: djarek  
> Created at: 2019-09-08 23:18:36 UTC  
> Url: https://github.com/boostorg/asio/issues/277#issuecomment-529252578  

`async_wait()` on a socket should only be used if you're using the socket in reactor-style code. `ip::tcp::socket::wait_error` doesn't mean that you'll wait until an error, but instead, you'll wait until there's OOB data on the socket (equivalent to the err_fd set in `select()`)

---

## Comment 2

> Username: wangjiangw  
> Created at: 2019-09-09 07:22:52 UTC  
> Url: https://github.com/boostorg/asio/issues/277#issuecomment-529333682  

> `async_wait()` on a socket should only be used if you're using the socket in reactor-style code. `ip::tcp::socket::wait_error` doesn't mean that you'll wait until an error, but instead, you'll wait until there's OOB data on the socket (equivalent to the err_fd set in `select()`)  
  
ok, thanks for your answer  
from your answer，i have a question，that is, once the handler has been called on wait_error, the error_code in handler will always be 0 ? or just in some situations ？

---

## Comment 3

> Username: djarek  
> Created at: 2019-09-11 22:10:01 UTC  
> Url: https://github.com/boostorg/asio/issues/277#issuecomment-530585963  

If you call `cancel()` on the socket the error will be non-zero say something like `operation aborted`. In general this will complete any time you get an OOB event on the socket. Some more info here: https://stackoverflow.com/a/591104

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:08:41 UTC  
> Url: https://github.com/boostorg/asio/issues/277#issuecomment-752292817  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#715](https://github.com/chriskohlhoff/asio/issues/715).
