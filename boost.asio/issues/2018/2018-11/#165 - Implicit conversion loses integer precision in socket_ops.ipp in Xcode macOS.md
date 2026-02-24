# #165 - Implicit conversion loses integer precision in socket_ops.ipp in Xcode macOS [Closed]

> Username: runesig  
> Created at: 2018-11-12 06:12:39 UTC  
> Updated at: 2020-12-30 00:57:01 UTC  
> Closed at: 2020-12-30 00:57:00 UTC  
> Url: https://github.com/boostorg/asio/issues/165  

Hi  
  
I am getting the warning:  
  
Implicit conversion loses integer precision: 'std::size_t' (aka 'unsigned long') to 'socklen_t' (aka 'unsigned int')  
  
int the file socket_ops.ipp when compiling ASIO with Xcode.  
  
<img width="1512" alt="screen shot 2018-11-12 at 07 10 11" src="https://user-images.githubusercontent.com/2069630/48329730-4ab2cb80-e64a-11e8-9d4f-8744ee83b8d1.png">

---

## Comment 1

> Username: pbhatkhande  
> Created at: 2019-01-25 15:40:24 UTC  
> Url: https://github.com/boostorg/asio/issues/165#issuecomment-457613990  

I have the same issue. Did you manage to fix this?

---

## Comment 2

> Username: runesig  
> Created at: 2019-01-25 16:31:51 UTC  
> Updated at: 2019-01-25 16:35:30 UTC  
> Url: https://github.com/boostorg/asio/issues/165#issuecomment-457631910  

No, not really. After some research I decided to turn the "Implicit Conversion To 32 bit" warning off. I have not experienced any issues by doing so.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:56:59 UTC  
> Url: https://github.com/boostorg/asio/issues/165#issuecomment-752290603  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#651](https://github.com/chriskohlhoff/asio/issues/651).
