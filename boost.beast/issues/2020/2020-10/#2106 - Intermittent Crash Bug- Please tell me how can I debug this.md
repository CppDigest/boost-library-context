# #2106 - Intermittent Crash Bug: Please tell me how can I debug this [Closed]

> Username: Raj123456788  
> Created at: 2020-10-19 23:23:58 UTC  
> Updated at: 2020-10-26 16:12:32 UTC  
> Closed at: 2020-10-26 16:12:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2106  

(4365,0x700008f2f000) malloc: *** error for object 0x10f599000: pointer being freed was not allocated  
(4365,0x700008f2f000) malloc: *** set a breakpoint in malloc_error_break to debug  
  
Also,  
Do I need to set below field to null after every response is returned?  
boost::beast::http::response<boost::beast::http::string_body, Fields> res;

---

## Comment 1

> Username: Raj123456788  
> Created at: 2020-10-20 17:21:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-713017322  

@madmongo1 : Can you please comment?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-10-20 17:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-713018787  

If you are going to re-use the response then you should assign an empty copy to it before re-use:  
  
```  
    res = {};  
```  
  
The malloc error you are seeing will be something else. It looks like you're freeing something that was already freed.

---

## Comment 3

> Username: Raj123456788  
> Created at: 2020-10-20 17:35:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-713024852  

@madmongo1 : Thanks for the response. But I am not freeing any malloc's it happens after every 20 mins or so. Is there any way I can debug this?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-10-20 19:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-713101966  

Every time you new/delete something there is a malloc/free involved.  
  
Are you able to post a link to a minimal program that replicates the issue?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-10-21 05:43:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-713317720  

Hi @Raj123456788 ,  
  
At the moment, I have sight of exactly one line of your program so it's difficult to comment meaningfully.  
  
A good place to start is by compiling one of the examples in the Beast documentation and running that. If you see the problem occur then you know its with your environment rather than your code. This is because our example code is tested extensively and known to be correct.  
  
If the problem doesn't happen with our examples then you know that the problem is something that you are doing wrong.  
I'd be happy to comment on any code you can show _after you have been throught the above steps_.  
  
If in that case you are able to show a minimal complete program that I can copy and compile, that demonstrates the problem then I will probably be able to tell you very quickly what is wrong and why.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-10-26 16:12:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2106#issuecomment-716654598  

Hi @Raj123456788,   
  
I'm going to close this issue for now. When you have a compilable example for me to look at, please feel free to open another.
