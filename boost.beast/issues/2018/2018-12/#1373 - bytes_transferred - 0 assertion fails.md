# #1373 - bytes_transferred > 0 assertion fails [Closed]

> Username: Bobface  
> Created at: 2018-12-17 17:38:49 UTC  
> Updated at: 2019-01-13 01:53:12 UTC  
> Closed at: 2019-01-13 01:53:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1373  

I am basically having the exactly same problem as described here: [Assertion failed: (bytes_transferred > 0)](https://github.com/boostorg/beast/issues/807).  
  
I am using a `websocket::stream<ssl::stream<tcp::socket>>` to connect to a ws server. After some time (can be minutes, hours or even days) the `bytes_transferred > 0` assertion fails:  
  
`#2  0x00007ffff398139a in __assert_fail_base (fmt=0x7ffff3b087d8 "%s%s%s:%u: %s%sAssertion %s' failed.\n%n", assertion=assertion@entry=0x555557004919 "bytes_transferred > 0", file=file@entry=0x5555570047a8 "/usr/local/include/boost/beast/websocket/impl/read.ipp", line=line@entry=496`  
  
I can also post the full gdb stack trace if that would help.  
  
Are there any updates regarding this bug/problem?   
  
Thanks in advance!  
  
Boost Version: 1.69  
Asio Version: 1.12.1  
Beast Version: 186  
gcc (Ubuntu 8.2.0-1ubuntu2~18.04) 8.2.0

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-18 00:30:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-448051120  

Investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-18 00:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-448051423  

> Are there any updates regarding this bug/problem?  
  
No. But I guess I can add configurable setting which will invoke the completion handler with an error instead of asserting? You could opt-in to it as a workaround.

---

## Comment 3

> Username: Bobface  
> Created at: 2018-12-18 09:38:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-448157610  

>   
>   
> > Are there any updates regarding this bug/problem?  
>   
> No. But I guess I can add configurable setting which will invoke the completion handler with an error instead of asserting? You could opt-in to it as a workaround.  
  
That would be great!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-12-18 15:59:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-448271021  

We think that this is very likely a bug in Asio for not handling `SSL_ERROR_ZERO_RETURN`. Treating it as an EOF would be the right thing to do. I have put together a patch for Asio:  
https://github.com/vinniefalco/boost.asio/commit/89feb2ada49540da09ade83bed9cd9d7c3a455ef  
  
If you could apply this patch, uncomment the line which logs to cerr, and adjust the line so it logs to wherever/however you log in your program, then it would be very helpful for us to know what the log output is if the problem happens again. It is possible you won't see the log line (or the assert in beast) because of the handling of `SSL_ERROR_ZERO_RETURN`.

---

## Comment 5

> Username: Bobface  
> Created at: 2018-12-19 11:59:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-448569814  

Thank you for the update! I have also investigated a little bit. I think the error only happens when I  `async_close` the websocket stream from inside a handler which has been scheduled with `asio::post`. I will try to find out more and report back.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-12-21 14:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449405556  

Did you try my patch?

---

## Comment 7

> Username: Bobface  
> Created at: 2018-12-21 18:33:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449464791  

I have installed it, waiting for the error to occur now.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-12-21 21:13:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449499791  

The suspense is killing me

---

## Comment 9

> Username: Bobface  
> Created at: 2018-12-22 18:34:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449589771  

Me too. It seems like a tough bug always disappears when you are trying to track it down .. :) I am logging `stderr` to a file, as soon as I get some output I will post it here.

---

## Comment 10

> Username: Bobface  
> Created at: 2018-12-23 11:27:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449630114  

The error happened now, however I only got the assert output from sterr which I posted in my question above (`Assertion bytes_transferred > 0 failed`). I double checked that the patch is installed correctly.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-12-23 12:57:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449634760  

What did `if(result <= 0) std::cerr << "result <= 0, ssl_error = " << ssl_error << std::endl;` print?

---

## Comment 12

> Username: Bobface  
> Created at: 2018-12-23 13:06:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449635342  

It didn't print anything, I only got the assertion message from stderr.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2018-12-23 13:14:01 UTC  
> Updated at: 2018-12-23 13:14:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449635718  

Hmm, but you did uncomment this line right?  
```  
 //if(result <= 0) std::cerr << "result <= 0, ssl_error = " << ssl_error << std::endl;  
```  
https://github.com/vinniefalco/boost.asio/commit/89feb2ada49540da09ade83bed9cd9d7c3a455ef#diff-9693888572396784daf4f0aded26ce3aR286

---

## Comment 14

> Username: Bobface  
> Created at: 2018-12-23 13:24:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449636270  

Yes I did, then I rebuilt boost and my application.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2018-12-23 13:30:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449636556  

Well that makes no sense at all.. in that asio file on line 287 is the only way that the function can return with the error cleared but if there's no output it means the `else` was not taken...

---

## Comment 16

> Username: vinniefalco  
> Created at: 2018-12-23 16:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449650061  

We need more info. Please try editing that `engine::perform` function to log as much information as possible when `*bytes_transferred==0`. For example what is the value of `sys_error`? Apparently our understanding of the function is lacking because I would have expected there to be some kind of error. So the more information that we can gather about the variables in that function when this condition occurs, the more likely we will be able to diagnose it.

---

## Comment 17

> Username: Bobface  
> Created at: 2018-12-23 19:41:57 UTC  
> Updated at: 2018-12-23 19:42:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449659350  

Dereferencing `bytes_transferred` resulted in segfaults, so I checked for `result <= 0` which seems to be more or less the same as `bytes_transferred`. However this spams the output with lines like this (at least 20 per second):  
` pending_output_before=0 result=-1 ssl_error=2 sys_error=0 pending_output_after=203 `  
  
Is there a better place where I could check for `result <= 0`?

---

## Comment 18

> Username: pdimov  
> Created at: 2018-12-23 22:12:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449666281  

`ssl_error=2` is SSL_ERROR_WANT_READ, which should be part of normal operation, so you could just skip the output in this case and see if anything else turns up.

---

## Comment 19

> Username: Bobface  
> Created at: 2018-12-25 10:47:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-449839178  

The application just crashed, this is the output:  
  
```  
pending_output_before=0 result=0 ssl_error=6 sys_error=0 pending_output_after=0  
pending_output_before=0 result=-1 ssl_error=5 sys_error=0 pending_output_after=0  
```  
  
The second line was printed directly before the output. The first line seems to be older.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-01-06 04:22:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-451714224  

Any update?

---

## Comment 21

> Username: Bobface  
> Created at: 2019-01-06 12:28:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-451737875  

Seems to be running fine so far!

---

## Comment 22

> Username: vinniefalco  
> Created at: 2019-01-13 01:53:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1373#issuecomment-453795284  

I'll go ahead and close this, the pull request to fix asio is here:  
https://github.com/boostorg/asio/pull/182
