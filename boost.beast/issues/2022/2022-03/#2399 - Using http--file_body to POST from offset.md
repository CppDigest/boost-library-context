# #2399 - Using http::file_body to POST from offset [Closed]

> Username: cgskumar77  
> Created at: 2022-03-24 23:23:41 UTC  
> Updated at: 2022-10-11 17:43:34 UTC  
> Closed at: 2022-10-11 17:43:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2399  

How can I POST a file from a given offset? I will be using a serializer with file_body.  
  
Use case: To restart a POST from where it was disconnected.  
The client would issue a HEAD and use the content-length value in the response as the offset to restart the POST from.   
  
Is this possible?  
  
Thanks.  
PS: Not necessarily a new Issue but looking for some guidance, if possible. Please point me to a mailing list if I should post there.

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-25 06:42:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2399#issuecomment-1078707521  

HI there @cgskumar77 , thanks for posting.  
If you would like interactive help, feel free to join us on the #beast channel of the cpplang slack https://cppalliance.org/slack/  
  
Currently the file_body form of the serializer does not offer an offset argument.  
In order to achieve what you want, I would do the following:  
  
1. open the file and memory-map it  
2. use the dynamic_body type to describe the sub-region of the memory-mapped region to be sent.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-08-30 16:06:53 UTC  
> Updated at: 2022-08-30 16:07:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2399#issuecomment-1231873140  

You should be able to do that like this:  
  
```cpp  
request<file_body> res(...);  
boost::system::error_code ec;  
res.body().file().seek(your_offset, ec);  
```  
  
See [here](https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__file/seek.html)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:56:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2399#issuecomment-1256860004  

@cgskumar77 Did this work?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-10-11 17:43:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2399#issuecomment-1275055702  

@cgskumar77 If that doesn't work, please reopen the issue.
