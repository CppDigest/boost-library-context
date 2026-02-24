# #1089 - Can not compile beast code anymore after I install boost 1.66 [Closed]

> Username: goflatworld  
> Created at: 2018-04-04 03:46:08 UTC  
> Updated at: 2018-04-10 23:57:47 UTC  
> Closed at: 2018-04-10 23:57:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1089  

Hi,  
  
I'm not sure if this is the right place to ask or should I ask boost. So if it is a incorrect place, please forgive me and let me know what's the best place to ask.  
  
The project compiles OK when I use old boost version and use beast separately. Now, since beast is integrated into boost in 1.66. I'm trying to migrate the code to pure boost. However, now I can not compile it anymore.   
  
The old code was:  
  
` beast::websocket::opcode op_`  
I then modifed it to below:  
  
`boost::beast::websocket::opcode op_`  
  
When I compile, I got this error:  
  
error: ‘opcode’ in namespace ‘boost::beast::websocket’ does not name a type boost::beast::websocket::opcode op_;

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-04-04 14:31:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1089#issuecomment-378620673  

`opcode` is no longer a public interface, please check the CHANGELOG.md for instructions on how to fix compile errors (look under Version 52):  
https://github.com/boostorg/beast/blob/develop/CHANGELOG.md

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-10 15:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1089#issuecomment-380153940  

Has this issue been resolved?

---

## Comment 3

> Username: goflatworld  
> Created at: 2018-04-10 23:48:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1089#issuecomment-380282258  

Hi Vinnie,  
Thanks for the follow up. Yes. I have modified the code. I removed opcode all together.  It worked.  
Cheers  
George  
   
  
    On Wednesday, 11 April 2018, 1:58:46 am AEST, Vinnie Falco <notifications@github.com> wrote:    
   
   
Has this issue been resolved?  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-04-10 23:57:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1089#issuecomment-380283821  

I'll go ahead and close the issue then, feel free to open new ones as needed
