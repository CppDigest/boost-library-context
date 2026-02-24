# #904 - Assertion failed in websocket/impl/write.ipp line 468 [Closed]

> Username: nguoithichkhampha  
> Created at: 2017-11-24 02:52:18 UTC  
> Updated at: 2017-12-31 20:48:31 UTC  
> Closed at: 2017-12-31 20:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/904  

### Version of Beast 79  
Sometimes I get Assertion failed in websocket/impl/write.ipp line 468  
Can you explain some cases make assert failed in this code ?   
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-24 11:45:54 UTC  
> Url: https://github.com/boostorg/beast/issues/904#issuecomment-346810872  

Yes, there was a bug that got fixed in a later version. Please use version 124:  
https://github.com/boostorg/beast/tree/v124

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-12-02 14:09:20 UTC  
> Url: https://github.com/boostorg/beast/issues/904#issuecomment-348694485  

Has this issue been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-31 20:48:30 UTC  
> Url: https://github.com/boostorg/beast/issues/904#issuecomment-354623279  

It seems this is resolved in the newer version of Beast, so I'll go ahead and close this. If the problem persists feel free to open a new issue, thanks!
