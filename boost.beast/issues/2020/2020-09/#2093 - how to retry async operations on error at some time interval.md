# #2093 - how to retry async operations on error at some time interval [Closed]

> Username: yaozongyou  
> Created at: 2020-09-18 12:29:23 UTC  
> Updated at: 2020-09-29 06:44:44 UTC  
> Closed at: 2020-09-29 06:44:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2093  

I am using beast to write some server, and this server will call other RESTful api.  calling other servers contains the following steps:  async_connect、async_write、async_read, error may occur in any of these steps,  I want to retry these steps many times, for example 3 times, at some time interval, for example, 5 seconds. So my question is how to retry, is there any standard ways of retrying to suggest？   
  
A method I can think out is combining these steps into a single composed operation, it is easier to retry a composed operation.  But combining these steps into a single composed operation  is not easy.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-18 13:15:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-694861094  

First you would need to decide on the rules around retry.  
  
What you are actually describing I think is two states. There is an outer "retry max x times" state and an inner "perform the rest operation" state. The rest operation state might exit with an event indicating "success" or some form of failure, which the "retry" state would consume.  
  
For me, this argues for 2 composed operations, with the outer RETRY operation instanciating and waiting on the inner REST operation.  
  
Again, in a low level library like Beast, this is not easy to standardise because we find that our users' requirements vary.  
  
It might be that what you are really looking for is a high level HTTP client library with sync/async versions of operations, and the ability to specify parameters like "max number of retries", "max number of redirects to follow" and so on.  
  
I am working on one of those, but it is nowhere near publication

---

## Comment 2

> Username: yaozongyou  
> Created at: 2020-09-18 13:23:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-694865069  

thanks for your quick reply.  
I agree with you that it is not easy to standardise, but can we provide some examples for doing this.  https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/examples.html  
Currently these beast examples are of some kind a little limited.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-18 13:36:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-694872071  

If there's an error on a socket you can't retry anyway, because the state of the connection is not well-defined in all cases. You have to close and re-connect (especially with TLS).

---

## Comment 4

> Username: yaozongyou  
> Created at: 2020-09-18 13:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-694874448  

> If there's an error on a socket you can't retry anyway, because the state of the connection is not well-defined in all cases. You have to close and re-connect (especially with TLS).  
  
yes, the steps of retry contains close and re-connect, retry need to from a fresh start.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-09-18 13:54:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-694882254  

@vinniefalco An example might have some utility for users?  
It's a real enough problem

---

## Comment 6

> Username: yaozongyou  
> Created at: 2020-09-20 08:00:00 UTC  
> Updated at: 2020-09-20 08:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2093#issuecomment-695757950  

@madmongo1 modeling after https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/using_io/writing_composed_operations/echo.html  
I just write some code for exploring async_write_read with retries, may you please have a look at this:  
https://gist.github.com/yaozongyou/d698d22cef5cfc1c253f03c7345a162a  
I have made a simple test, it worked as expected.  
By the way, I am using `g++ (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3)`. So I am using decltype(auto) instead of trailing return type as in the echo example.
