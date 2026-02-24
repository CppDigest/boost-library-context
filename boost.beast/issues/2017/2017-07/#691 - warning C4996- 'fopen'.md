# #691 - warning C4996: 'fopen' [Closed]

> Username: ghost  
> Created at: 2017-07-28 08:01:48 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/691  

Hi, there is a warning popping up again:  
  
beast\include\boost/beast/core/impl/file_stdio.ipp(99): warning C4996: 'fopen': This function or variable may be unsafe. Consider using fopen_s instead. To disable deprecation, use _CRT_SECURE_NO_WARNINGS. See online help for details.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-28 14:10:32 UTC  
> Url: https://github.com/boostorg/beast/issues/691#issuecomment-318662075  

Please provide:  
* Version of Beast  
* Steps necessary to reproduce the problem  
* All relevant compiler information  
  
Are you building the tests or your own app?

---

## Comment 2

> Username: ghost  
> Created at: 2017-07-28 15:52:16 UTC  
> Url: https://github.com/boostorg/beast/issues/691#issuecomment-318690690  

I am building my own program with the latestest sources (master) and compiled with Visual Studio 2015.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-28 16:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/691#issuecomment-318693042  

@MartinKunz If you would like to cherry-pick this commit to try it: https://github.com/vinniefalco/beast/commit/9010b9ef1ae02cc98e551f6eb8fe6952de81e08b  
I think it will resolve your issue. This will be merged in **version 91**.  
  
So, you're using the `file_body`? Did you know that on Windows it uses `::TransmitFile` for performance? I am very interested in hearing about your experience with it, I don't know anyone who has used this feature yet so anything you can tell me about how it works (or how it doesn't work) would be quite helpful.  
  
You can verify that it is using `::TransmitFile` by logging or putting a breakpoint in these places:  
  
Asynchronous:  
https://github.com/boostorg/beast/blob/c7b830f37f8adc0df63d41ff4d31395ab704516b/include/boost/beast/http/impl/file_body_win32.ipp#L436  
  
Synchronous:  
https://github.com/boostorg/beast/blob/c7b830f37f8adc0df63d41ff4d31395ab704516b/include/boost/beast/http/impl/file_body_win32.ipp#L525  
  
Thanks!!
