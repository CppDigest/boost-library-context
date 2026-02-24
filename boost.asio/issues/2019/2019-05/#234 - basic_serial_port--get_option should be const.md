# #234 - basic_serial_port::get_option should be const [Closed]

> Username: Superlokkus  
> Created at: 2019-05-06 10:59:17 UTC  
> Updated at: 2019-10-14 09:18:56 UTC  
> Closed at: 2019-10-14 09:18:56 UTC  
> Url: https://github.com/boostorg/asio/issues/234  

As for every get method I propose basic_serial_port::get_option should be const.  
However if there are side effects which would make the omission of the const qualification legit, I propose to document them.

---

## Comment 1

> Username: Superlokkus  
> Created at: 2019-10-14 09:18:55 UTC  
> Url: https://github.com/boostorg/asio/issues/234#issuecomment-541572043  

Closed by 8a8d83fb2d997c6c26d5fdfe4eb086856fcd4430
