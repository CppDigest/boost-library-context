# #312 - Is there support for basic authentication [Closed]

> Username: ghost  
> Created at: 2017-04-21 03:29:52 UTC  
> Updated at: 2017-04-28 00:41:32 UTC  
> Closed at: 2017-04-28 00:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/312  

This looks very interesting. I am looking at building a REST client using Beast. I read through the documentation and examples and I don't see this - is basic authentication supported? Or do I need to encode the user/password in the header and then manage the challenge/response?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-21 16:28:20 UTC  
> Updated at: 2017-04-21 16:28:28 UTC  
> Url: https://github.com/boostorg/beast/issues/312#issuecomment-296238503  

You have to manage the header fields yourself. Consider making your code into a library that other people can use!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-04-28 00:41:32 UTC  
> Url: https://github.com/boostorg/beast/issues/312#issuecomment-297876777  

It looks like this issue is resolved, I'll go ahead and close it. If you have another issue, or you think that there is more to this issue that needs to be discussed please feel free to open a new issue or re-open this one, thanks!
