# #439 - In examples/file_body.hpp, buf_len_ is essentially unused [Closed]

> Username: niklas88  
> Created at: 2017-06-08 09:50:53 UTC  
> Updated at: 2017-06-09 22:07:47 UTC  
> Closed at: 2017-06-09 22:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/439  

On master in examples/file_body.hpp get() buf_len_ is set to the number of bytes to be read into the buffer in ideal conditions but it is never actually used. I think it should either be used in the call to fread or dropped completely. In the end the number of bytes actually read is what really matters.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 12:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/439#issuecomment-307091132  

Ah, you are correct! I love removing code!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-08 20:05:53 UTC  
> Url: https://github.com/boostorg/beast/issues/439#issuecomment-307212445  

@niklas88 Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!
