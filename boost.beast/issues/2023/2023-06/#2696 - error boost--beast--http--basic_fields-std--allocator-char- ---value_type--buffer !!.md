# #2696 - error boost::beast::http::basic_fields<std::allocator<char> >::value_type::buffer !! [Closed]

> Username: yasastharinda9511  
> Created at: 2023-06-06 18:38:56 UTC  
> Updated at: 2023-10-28 16:32:15 UTC  
> Closed at: 2023-10-28 16:32:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2696  

Got this error for higher request rates(for lower request rates it's fine)  
 boost::beast::http::basic_fields<std::allocator<char> >::value_type::buffer (this=0x7ff86ff8aa68) at /usr/include/boost/beast/http/impl/fields.hpp:287  
287	        static_cast<std::size_t>(off_) + len_ + 2};  
  
which occurs when I try to set headers like this req->set(pair_ref.first , beast::string_view(pair_ref.second)); , After commenting this line works fine !!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-07 03:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1579795850  

What's pair_ref and are you doing this multi-threaded? Did you run any sanitizer?

---

## Comment 2

> Username: yasastharinda9511  
> Created at: 2023-06-07 03:15:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1579808868  

Yeah I'm doing this in multi-threaded environment. Here pair_ref is std::pair<std::string, std::string> (even for the hard coded values this occurs) . no I didn't

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-06-07 05:16:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1579912263  

Are you calling .set from different threads? That would be a race condition and UB.

---

## Comment 4

> Username: yasastharinda9511  
> Created at: 2023-06-08 13:51:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1582615981  

Yes I passed the .set to the "boost::asio::post(_io_context.get_executor(), handler)". the matter is if I commented out that line and run for higher request rate works fine. if it is a race condition after the commenting that line also should encounter the same error . but it does not .

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-06-08 14:02:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1582635383  

Well that depends on whether or not your io_context is running on multiple threads, in which case the post would introduce a race condition.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-19 05:18:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2696#issuecomment-1684824432  

@yasastharinda9511 Is this still open?
