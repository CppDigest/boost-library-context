# #219 - "beast/http/basic_fields.hpp" file not found. [Closed]

> Username: igorland  
> Created at: 2017-01-05 13:22:58 UTC  
> Updated at: 2017-01-10 23:48:16 UTC  
> Closed at: 2017-01-10 23:48:16 UTC  
> Url: https://github.com/boostorg/beast/issues/219  

Hello. Thanks a lot for your efforts. I am trying to include Beast into my project (Xcode on Mac).  
  
I have copied the folder "beast" into my project, then included "/path/to/include/beast/" in the Header Search Paths setting of the project. Then I tried one of your samples. I get the error that "beast/http/basic_fields.hpp" file not found. I checked -- the file is physically there.   
  
Any idea what this may be caused by? (FYI, boost has been built successfully and tested.)  
  
Thank you!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-05 13:40:58 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270646269  

hmm...could this be a problem with case sensitivity?

---

## Comment 2

> Username: igorland  
> Created at: 2017-01-06 04:12:56 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270827414  

Hi. I have managed to link the library; however I get the following errors (image):  
  
<img width="1280" alt="screen shot 2017-01-05 at 11 09 40 pm" src="https://cloud.githubusercontent.com/assets/16550440/21707332/722602e4-d39c-11e6-9eb2-ecb31f9ad609.png">  
  
Thanks a lot!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-06 04:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270827907  

hmm... your program is using an older interface, the field "headers" has been renamed to "fields". See this source file:  
https://github.com/vinniefalco/Beast/blob/master/examples/http_example.cpp  
  
Looking over the Beast README.md, I see that I forgot to update it! I will fix it shortly, sorry about that.

---

## Comment 4

> Username: igorland  
> Created at: 2017-01-06 04:26:22 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270828659  

Works as a charm now. Thanks a lot! I will now include it in my real project (which is multi-platform) and will report whether there will be any issues.  
Thank you so much for your great help!  
Igor

---

## Comment 5

> Username: igorland  
> Created at: 2017-01-06 04:34:54 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270829428  

One question. I am trying to download a binary file from an http site and save it on my hard drive rather than reading it. Is it possible? Many thanks!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-01-06 13:07:53 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-270900188  

Yes, that is possible. You need to create your own **Body** type and define a custom **writer**:. See:  
http://vinniefalco.github.io/beast/beast/ref/Body.html  
http://vinniefalco.github.io/beast/beast/ref/Writer.html

---

## Comment 7

> Username: igorland  
> Created at: 2017-01-07 02:32:40 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-271056806  

vinniefalco. Thank you. This is very encouraging as I would really prefer Beast to libcurl. I looked at the links that you provided but it is still a bit unclear how to use your library to download and save the file on the hard drive. Unfortunately, I was not able to find anything online either. Do you have any specific examples? Thank you so much!

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-01-07 02:47:17 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-271057605  

@igorland We need to be realistic here - Beast is not a replacement for libcurl. It won't remove gzip-encoding or multi-part encoding. It won't handle redirects. Its a low level library.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-01-07 13:22:02 UTC  
> Updated at: 2017-01-07 13:22:09 UTC  
> Url: https://github.com/boostorg/beast/issues/219#issuecomment-271083537  

Here's an example of a **Body** with associated **writer**  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/basic_dynabuf_body.hpp#L64
