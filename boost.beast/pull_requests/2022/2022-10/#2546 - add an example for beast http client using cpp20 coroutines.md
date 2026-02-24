# #2546 add an example for beast http client using cpp20 coroutines [Closed]

> Username: yuvalif  
> Created at: 2022-10-18 16:05:38 UTC  
> Updated at: 2022-10-29 20:33:27 UTC  
> Closed at: 2022-10-29 20:33:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2546  

to test the asyncrony of the client an async http server should be used for example: https://gist.github.com/yuvalif/04da5f60e43fad2da676eb8efced205d  
  
Signed-off-by: Yuval Lifshitz <ylifshit@redhat.com>

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-10-18 16:16:01 UTC  
> Url: https://github.com/boostorg/beast/pull/2546#issuecomment-1282653208  

There's an open PR here #2538, which should make it into the next release. I'd be grateful if you took a look,.  
  
Besides that, there are a few odd choices on your code:  
  
1. you use redirect_error to then just rethrow the exception. I don't see why you would do that.  
2. Throwing fomr a completion handler is a bad way to handle exceptions in asio, since it shuts down the entire io-context. This ain't the way asio does exception handling.  
3. Why are you doing multiple gets? What's the point in getting the same thing multiple times?

---

## Comment 2

> Username: yuvalif  
> Created_at: 2022-10-19 09:41:19 UTC  
> Url: https://github.com/boostorg/beast/pull/2546#issuecomment-1283719822  

thanks for the feedback!  
  
> There's an open PR here #2538, which should make it into the next release. I'd be grateful if you took a look,.  
>   
sure. will look there.  
  
i saw that there were changes in that PR not only to the examples but also to the basic_stream.hpp and buffers_generator.hpp.  
are these changes necessary for the beast code to work with cpp20 coroutines?  
  
> Besides that, there are a few odd choices on your code:  
>   
> 1. you use redirect_error to then just rethrow the exception. I don't see why you would do that.  
  
no specific reason. what would be better handling if i want the program to bail out of the failed operation and continue to the next one?  
  
> 2. Throwing fomr a completion handler is a bad way to handle exceptions in asio, since it shuts down the entire io-context. This ain't the way asio does exception handling.  
  
[in your code](https://github.com/boostorg/beast/pull/2538/files#diff-4497c0b041a4ca43f80e44740ff2846374337d997bd164922e269cfffbdba817R135) you also throw from the completion handler of `co_spawn()`. what would be the right way for handling exceptions thrown in the coroutines?  
  
> 3. Why are you doing multiple gets? What's the point in getting the same thing multiple times?  
  
I wanted to make sure that things are running in parallel, I will be using the client for server2server communications.  
with a single request, this cannot be verified (btw, the requests are different, as the index to the URL).  
for testing, I used an [HTTP server that injects delays](https://gist.github.com/yuvalif/04da5f60e43fad2da676eb8efced205d)

---

## Comment 3

> Username: yuvalif  
> Created_at: 2022-10-29 20:33:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2546#issuecomment-1295965824  

not needed. examples were added here: https://github.com/boostorg/beast/pull/2538

---
