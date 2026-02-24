# #2094 - AddressSanitizer shows heap-buffer-overflow on geting reason().data() from the http response object. [Closed]

> Username: ashjas  
> Created at: 2020-09-18 14:34:19 UTC  
> Updated at: 2020-11-16 03:45:55 UTC  
> Closed at: 2020-11-16 03:45:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2094  

Beast version 1.71  
  
We are using beast in our application for REST communication.  
When we run our application with AddressSanitizer integrated, we get  a heap-buffer-overflow at the 2nd line  
shown below.  
  
We are using the following http response object  
```boost::optional<http::response_parser<http::string_body>> m_res;```  
After we get the data back, we return the data back to the caller, one of which is where we   
copy the status message from the response object like this:  
```respPtr->setStatusMessage(m_res.get().get().reason().data());```  
  
any idea why could this could end up in  heap-buffer-overflow error?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-09-18 14:45:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2094#issuecomment-694911756  

Hi @ashjas ,  
  
It's difficult for me to reconstruct your entire program from two lines, so hard to say :-)  
  
What I think your question means is, "who's fault is this?"  
  
My initial assumption would be, "yours".  
  
But if you can give me a program that I can compile to reproduce the issue, I'm prepared to admit that it's, "mine" and would be happy to provide a fix.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-09-18 14:49:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2094#issuecomment-694913804  

Please bear in mind that the return type of `reason()` is a `string_view` and is therefore not guaranteed to be null-terminated.  
  
I suspect your use of `reason().data()` is a logic error.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-11-01 07:41:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2094#issuecomment-720048930  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2020-11-16 03:45:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2094#issuecomment-727717735  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
