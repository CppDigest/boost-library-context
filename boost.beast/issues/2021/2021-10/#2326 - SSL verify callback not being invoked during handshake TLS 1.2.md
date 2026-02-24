# #2326 - SSL verify callback not being invoked during handshake TLS 1.2 [Closed]

> Username: gopalak  
> Created at: 2021-10-09 16:52:02 UTC  
> Updated at: 2024-01-01 09:12:19 UTC  
> Closed at: 2024-01-01 09:12:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2326  

Beast version 306  
I understand this is not a beast specific issue. But my TLS client code has a ssl verify callback set on the SSL context but that never gets invoked. Searching around I find different answers but nothing seems to explain why a callback for cert verification is not being called.  
  
I have set the verify mode to verofy_peer  
set the verify call back function as well  
  
Appreciate the help  
PS: I know the server I connect to presents a self signed cert

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-10-09 16:52:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-939326797  

@chriskohlhoff Any Ideas?

---

## Comment 2

> Username: gopalak  
> Created at: 2021-10-09 16:53:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-939326878  

> @chriskohlhoff Any Ideas?  
  
I did post the same in that repo as well. Havent seen reply :(

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-10-09 19:22:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-939349712  

> > @chriskohlhoff Any Ideas?  
>   
> I did post the same in that repo as well. Havent seen reply :(  
  
Chris is a busy guy.

---

## Comment 4

> Username: brjoha  
> Created at: 2021-11-20 23:42:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-974727337  

Perhaps post your code that is constructing the SSL context?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-1008220684  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:02:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2326#issuecomment-1256861611  

@gopalak any luck on providing us with some code?
