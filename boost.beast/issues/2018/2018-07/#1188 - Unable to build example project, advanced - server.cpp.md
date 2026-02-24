# #1188 - Unable to build example project, advanced - server.cpp [Closed]

> Username: ChristopherO1  
> Created at: 2018-07-12 14:57:05 UTC  
> Updated at: 2018-08-13 07:40:56 UTC  
> Closed at: 2018-08-13 07:40:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1188  

This more likely down to my limited experience, rather than a bug.  
  
I can't compile the project found here   
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp  
  
My issue is in class queue within class http_session :  
  
template<bool isRequest, class Body, class Fields>  
void operator()(http::message<isRequest, Body, Fields>&& msg)  
{  
struct work_impl : work  
            {  
               http_session& self_;  - **_line the compiler is grumbling about_**  
                http::message<isRequest, Body, Fields> msg_;  
  
                work_impl(  
                    http_session& self,  
                    http::message<isRequest, Body, Fields>&& msg)  
                    : self_(self)  
                    , msg_(std::move(msg))  
                {  
                }                  
            };  
}  
  
  
**_Error	C2530	'self_': references must be initialized	WebSocket_**  
  
Using  
Windows 7   
VS 2017 community  
I have BOOST 1.67.0 and the vc141 libraries  
  
I have limited experience with Templates, so not honestly sure what I need to do to resolve this.  
  
Any help gratefully received  
  
Thank you  
  
Chris

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-12 15:14:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-404547582  

Are you using `/permissive-` ?

---

## Comment 2

> Username: ChristopherO1  
> Created at: 2018-07-12 15:17:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-404548571  

I'm guessing not, would that be a properties option?  
Where would I need to set it?

---

## Comment 3

> Username: ChristopherO1  
> Created at: 2018-07-12 15:20:07 UTC  
> Updated at: 2018-07-12 15:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-404549499  

It's OK I found it.  
Added it.  
No change unfortunately  
  
Apparently it's set by default in VS 2017 Version 15.7.5

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-07-12 15:45:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-404558211  

What I mean is that `/permissive-` seems to cause the problem. You should try un-setting it (not sure how to do that).  
  
> Apparently it's set by default in VS 2017 Version 15.7.5  
  
How do you turn it off?

---

## Comment 5

> Username: ChristopherO1  
> Created at: 2018-07-13 07:51:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-404755653  

Found it. it's in the properties  
C/C++  
All Options - Conformance Mode: Set to No  
  
Build succeeded :)  
  
Thanks for the pointers, much appreciated

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-08-12 07:57:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-412325676  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: ChristopherO1  
> Created at: 2018-08-13 07:40:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1188#issuecomment-412433993  

Yes. Sorry  
Now closed
