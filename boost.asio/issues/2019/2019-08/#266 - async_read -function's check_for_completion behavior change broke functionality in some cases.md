# #266 - async_read -function's check_for_completion behavior change broke functionality in some cases [Closed]

> Username: mpietarin  
> Created at: 2019-08-08 14:04:02 UTC  
> Updated at: 2020-12-30 01:07:58 UTC  
> Closed at: 2020-12-30 01:07:57 UTC  
> Url: https://github.com/boostorg/asio/issues/266  

async_read -function (at least some version) goes into boost_1_70_0\boost\asio\impl\read.hpp -file and there in read_op::operator()(const boost::system::error_code& ec, std::size_t bytes_transferred, int start = 0)  -method. This method have been changed after boost version 1.61.0 (actual commit is in my opinion the "Initial merge of Networking TS compatibility" 4a4d28b0d24c53236e229bd1b5f378c9964b1ebb). I had my async_read using system working just fine. Then I updated to boost version 1.70.0 and my async_read didn't work anymore.  
Primary problem for me was that read_op::operator -method used to do following in default case in this "switch and for-loop mixture" -clause:  
  1) check that there is no errors and something has been read  
  2) call for check_for_completion(ec, total_transferred_) -function  
  3) check if buffer has been read totally  
So this ensured that the check_for_completion -function was called if no errors occurred, and my version of check_for_completion -function made some status changes in my application.  
  
New code does following:  
  1) Check for errors (break from loop)  
  2) OR Check if buffer has been read empty (break from loop)  
  3) Only after 1+2 checks, call for check_for_completion(ec, total_transferred_) -function  
So the new code doesn't call the check_for_completion -function always/ever(?) at least the way my application uses asio.  
  
Because my application handles distributed calculation between client <-> master <-> multiple workers (they are all separate processes), these kind of problems can be very difficult to figure out what is going on. It took me whole week to understand the problem and I already made crude quick-fix for it so that my application does separate state settings in case the check_for_completion -function was not called.

---

## Comment 1

> Username: youngwolf-project  
> Created at: 2019-08-15 08:26:20 UTC  
> Url: https://github.com/boostorg/asio/issues/266#issuecomment-521559605  

If the socket has became unavailable to read more data, calling check_for_completion is useless (no matter what value it returns, no more reading can be performed anymore but just call the read handler, right?), so I believe it's a meaningful optimization.  
I met this problem before too, I changed my code to support this situation -- no check_for_completion invocation before the read handler been invoked.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:07:56 UTC  
> Url: https://github.com/boostorg/asio/issues/266#issuecomment-752292696  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#709](https://github.com/chriskohlhoff/asio/issues/709).
