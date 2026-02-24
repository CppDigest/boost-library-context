# #2996 - Question while using buffer_body parser with async_read [Closed]

> Username: albxy  
> Created at: 2025-03-27 14:35:24 UTC  
> Updated at: 2025-12-13 14:52:28 UTC  
> Closed at: 2025-04-04 14:41:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2996  

I tried to copy HTTP Relay & Incremental Read example,but strangely it doesn't work.It seems it does not read at all.  
Please see the full code [here](https://github.com/albxy/simple-web-server) and focus on "uploads.h" and "main.cpp".

---

## Comment 1

> Username: ashtum  
> Created at: 2025-03-27 14:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2758360199  

The parser and buffer must be reused after calling `async_read_header` for the subsequent `async_read` call to read the body. It looks like you are moving the parser instance to a stack variable and then calling `async_read`, which causes it to be destroyed before `async_read` attempts to access it.  
  
Note that the example in the documentation uses the synchronous API for simplicity. If you want to use the asynchronous API, you need to restructure your code accordingly.

---

## Comment 2

> Username: albxy  
> Created at: 2025-03-28 05:25:59 UTC  
> Updated at: 2025-12-13 14:52:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2760229023  

Sorry that I don't really understand "moving the parser instance to a stack variable and then calling async_read"Could you please be more specific? Also,how to resturcture the code?I mean,where have I used some structures that should be a synchornous way?

---

## Comment 3

> Username: ashtum  
> Created at: 2025-03-28 05:52:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2760266914  

> Sorry that I don't really understand "moving the parser instance to a stack variable and then calling async_read"Could you please be more specific?  
  
https://github.com/albxy/simple-web-server/blob/main/main.cpp#L176  
  
> Also,how to resturcture the code?I mean,where have I used some structures that should be a synchornous way?  
  
No, I mean you need to manage the lifetime of arguments passed to initiation functions like `async_read`, such as the reference to parser and buffer. Since these operations complete at a later time, you can't put objects on the stack and pass references to them (they will be destroyed when these operations try to access them).  
  
It would be easier to start with a working example like [http_client_async.cpp](https://github.com/boostorg/beast/blob/develop/example/http/client/async/http_client_async.cpp) and try to change it step by step. Here is an slightly modifed version that reads header and body separately: https://gist.github.com/ashtum/55736ea8557cc2a6a6628cb6f18d9336

---

## Comment 4

> Username: albxy  
> Created at: 2025-03-28 09:28:38 UTC  
> Updated at: 2025-12-13 14:52:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2760697026  

Thanks a lot for your advice!However,the other user actions can work very well.(line 180 - 190 in main.cpp) They also use the line 176.

---

## Comment 5

> Username: ashtum  
> Created at: 2025-03-28 09:36:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2760719533  

That's what undefined behavior is, your code might produce the expected results in some situations.  
You can try running your code in a debugger to observe the parser's lifetime. You can also use Valgrind or an address sanitizer to check for invalid accesses.

---

## Comment 6

> Username: albxy  
> Created at: 2025-03-28 14:32:21 UTC  
> Updated at: 2025-12-13 14:52:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2761539931  

I think I get it!So I made some changes in "main.cpp" and "uploads.h",prevented the use of"...move...".But it still didn't work...If necessary I will check the lifetime of the parser,but it's a bit hard for me to check it on windows...Anyway,I will try my best to solve the bug^_^

---

## Comment 7

> Username: albxy  
> Created at: 2025-03-29 10:32:33 UTC  
> Updated at: 2025-12-13 14:52:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2763284950  

I understand the liftime of parser.We need to make sure all the process should be done in the async_read's  handler(callback function?).That's why this[pretty little thing](https://gist.github.com/ashtum/55736ea8557cc2a6a6628cb6f18d9336) works,because it binds the handler to the on_read.It's so happy to find out he problem,but now there is another big problem:"How to move my procedeure of handling upload requests into a Recursion one".Or is there a way to keep the present sturcture?  
  
Also,http::request_parser<http::string_body> parser{ std::move(*parser_) };//It should work,due to https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/more_examples/change_body_type.html

---

## Comment 8

> Username: albxy  
> Created at: 2025-03-30 12:16:00 UTC  
> Updated at: 2025-12-13 14:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2764531605  

There are still some bugs.I think I haven't completely understand the lifetime of parser:When I tried to call it in the handle_post_request function,it has already been destroyed?Please take a look at [the new code](https://github.com/albxy/simple-web-server).

---

## Comment 9

> Username: ashtum  
> Created at: 2025-03-31 11:47:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2765984203  

> There are still some bugs.I think I haven't completely understand the lifetime of parser:When I tried to call it in the handle_post_request function,it has already been destroyed?Please take a look at [the new code](https://github.com/albxy/simple-web-server).  
  
Because you are still leaving the parser instance on the stack:  
https://github.com/albxy/simple-web-server/blob/main/main.cpp#L180  
  
You need to keep the parser alive until `async_read` finishes (by making it a member variable), so instead of:  
```C++  
boost::optional<http::request_parser<http::buffer_body>> parser_;  
```  
You should use:  
```C++  
http::request_parser<http::buffer_body> parser1_;  
boost::optional<http::request_parser<http::string_body>> parser2_;  
```  
And when you want to change the body type:  
  
```C++  
parser2_.emplace(std::move(parser1_));  
http::async_read(stream_, buffer_, parser2_, ...  
```

---

## Comment 10

> Username: albxy  
> Created at: 2025-03-31 14:34:45 UTC  
> Updated at: 2025-12-13 14:52:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2766441001  

I am very sorry to mislead you.Actually,the handle_post_request eorks very well.The problem occured in handle_upload_requset.As what I have comprehended,all the process of the request should be done in the callback of async_read(_header).[https://gist.github.com/ashtum/55736ea8557cc2a6a6628cb6f18d9336](https://gist.github.com/ashtum/55736ea8557cc2a6a6628cb6f18d9336)  
So please take a look at the new class I have defined.It says"read:bad method"even when I tried to read in the handle_upload_requset function directly.  
Is there another way that can solve this "callback problem"?I think this kind of way might ocst too much,since it is a sort of"Recursion"?  
The liftime of it is really weird...Will it be unavailable after just using "&" to pass it to a function?

---

## Comment 11

> Username: albxy  
> Created at: 2025-04-01 14:20:27 UTC  
> Updated at: 2025-12-13 14:52:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2769546947  

Maybe we should add more example using asynchornous APIs since the difference between async and sync is huge.I believe there will be more who just simply replace sync APIs with async APIs.

---

## Comment 12

> Username: albxy  
> Created at: 2025-04-04 14:41:38 UTC  
> Updated at: 2025-12-13 14:52:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2996#issuecomment-2778934957  

The problem is solved.Though the way is starnge(probably at a high cost?)Please take a look at [the new code](https://github.com/albxy/simple-web-server)
