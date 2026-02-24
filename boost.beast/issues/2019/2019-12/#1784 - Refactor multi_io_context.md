# #1784 - Refactor multi_io_context [Open]

> Username: vinniefalco  
> Created at: 2019-12-19 14:54:49 UTC  
> Updated at: 2022-09-24 05:50:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1784  

This class could use a rewrite and also I should probably live-stream the changes.

---

## Comment 1

> Username: benstadin  
> Created at: 2020-06-15 23:32:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644443634  

+1 I‘m interested to further boost performance for multithreaded servers. This seems to be a bottle neck in my application as the connections per second do not scale beyond a certain limit, regardless of CPU cores available.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-06-15 23:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644444227  

> connections per second  
  
Do you mean the number of incoming connections per second? If you want to maximize that number you need multiple listening sockets, all listening on the same port (and the appropriate socket options set). Note that I am by no means an expert at this.

---

## Comment 3

> Username: benstadin  
> Created at: 2020-06-15 23:40:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644446025  

Yes, that‘s what I actually mean. I‘ve tried some code snippets I found in one of your repos (though as I remember those snippets were from somebody else), but gave up on it since it required some deeper insights into ASIO and Beast. I thought this is related.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-15 23:41:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644446396  

None of my examples address getting a high number of received connections, and I don't have any experience with it, except that in order to get high rates you need multiple listening sockets.

---

## Comment 5

> Username: benstadin  
> Created at: 2020-06-16 00:06:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644453429  

I‘ve found our previous conversation [1]. If I understand correctly, for maximum throughput the best would be to use something like number of CPU cores for the multi IO contexts and each context has its own listening socket?  
  
[1] https://github.com/boostorg/beast/issues/1285

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-06-16 00:24:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-644458723  

I'm not sure, you would have to experiment, You might even need multiple listening sockets for each `io_context`.

---

## Comment 7

> Username: AmitArie  
> Created at: 2020-10-23 17:37:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-715479025  

Hey guys!  
There is something new about this subject?  
The idea sounds very interesting!  
  
I also want to find a way to utilize the amount of cores my server has, and to support massive amount of connections per second.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-10-23 17:39:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-715480081  

There are some videos of Chris Kohlhoff giving talks on just this subject our there on the internet.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-10-23 22:17:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-715614106  

I have some code here  
https://github.com/vinniefalco/beast/tree/multi-io/include/boost/beast/_experimental/core

---

## Comment 10

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:50:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1784#issuecomment-1256873180  

Shuoldn't this just be done with `asio::thread_pool` nowadays?
