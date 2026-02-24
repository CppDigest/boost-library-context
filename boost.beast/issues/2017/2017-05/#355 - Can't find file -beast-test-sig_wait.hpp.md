# #355 - Can't find file "beast/test/sig_wait.hpp" [Closed]

> Username: jchdng  
> Created at: 2017-05-11 05:46:27 UTC  
> Updated at: 2017-05-13 23:12:09 UTC  
> Closed at: 2017-05-13 23:12:09 UTC  
> Url: https://github.com/boostorg/beast/issues/355  

I am trying the sample code to write a http server ,but got a compileing error message "fatal error: beast/test/sig_wait.hpp: No such file or directory".I think the examples may be too old to compile.I am learning the beast but I failed to compile the doc to a html document, and I want to know if I can get a complied document for learning.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-11 13:00:40 UTC  
> Url: https://github.com/boostorg/beast/issues/355#issuecomment-300781712  

How are you compiling the sample code? Are you using the build scripts which come with Beast? What version of Beast are you using? The HTML documentation can be viewed here:  
http://vinniefalco.github.io/beast/index.html  
  
You said you failed to compile the doc, did you follow the instructions in the README? What error did you get?

---

## Comment 2

> Username: jchdng  
> Created at: 2017-05-12 01:14:07 UTC  
> Url: https://github.com/boostorg/beast/issues/355#issuecomment-300957643  

I just copied some file and code from the example folder to my own project and got the error message.I'v compiled the example folder's sample with b2 successful.Thanks for you instructions!

---

## Comment 3

> Username: jchdng  
> Created at: 2017-05-12 02:36:41 UTC  
> Url: https://github.com/boostorg/beast/issues/355#issuecomment-300968413  

I found the file path is located at extras,and the sample code compiled ok!Thanks.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-05-13 23:12:09 UTC  
> Url: https://github.com/boostorg/beast/issues/355#issuecomment-301280292  

Looks like this has been resolved! If you have more questions please feel free to open new issues, thanks!
