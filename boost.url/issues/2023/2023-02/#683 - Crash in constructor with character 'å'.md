# #683 - Crash in constructor with character 'å' [Closed]

> Username: Dangeroustuber  
> Created at: 2023-02-13 10:45:53 UTC  
> Updated at: 2023-02-13 14:19:25 UTC  
> Closed at: 2023-02-13 14:19:24 UTC  
> Url: https://github.com/boostorg/url/issues/683  

Hello, i have a HTTPS server where I'm trying to construct a URL object with the following std::string:   
```cpp  
?limit=100&query=eq(RpOmrÃ¥de/arealplanid/planidentifikasjon,63280000)  
```  
  
I'm using postman to send the request to the server and it sends the string like this to the server:   
```cpp  
?limit=100&query=eq(RpOmr%C3%A5de/arealplanid/planidentifikasjon,63280000)  
```  
  
code snippet:  
```cpp  
auto par = boost::url{query_parameters}; // code that is throwing  
```  
  
this is the exception message when i catch it with a general exception: `leftover [boost.url.bnf:4]`  
  
I've tried turning the string from UTF8 to regular std::string where the 'å' is represented correctly but it still crashes in the constructor.  
If you try to construct it like this: "limit=100&query=eq(RpOmrade/arealplanid/planidentifikasjon,63280000)" without the 'å' character, that does not throw any error.  
  
This is using the 1.80 version and i'm using MSVC on windows. Thanks for any help and let me know if you need any more information.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-13 11:00:22 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427739740  

This program throws, because URLs cannot contain high-ascii characters:  
  
https://godbolt.org/z/KE6qjeG9K  
  
This program does not throw, as it contains only valid characters:  
  
https://godbolt.org/z/zErv3Yh1P  
  
You said:  
> I've tried turning the string from UTF8 to regular std::string where the 'å' is represented correctly but it still crashes in the constructor.  
  
Can you please post a small, complete program on Compiler Explorer that crashes, which I can run myself to see the problem?

---

## Comment 2

> Username: Dangeroustuber  
> Created at: 2023-02-13 12:04:08 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427827556  

Here is a godbolt link: https://godbolt.org/z/GqqcWa686  
I could not get std::cout to work but there is probably a way to view the fixed string. It should have the actual 'å' in it.  
  
Thanks again.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-02-13 13:29:42 UTC  
> Updated at: 2023-02-13 13:50:06 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427946927  

I think there's a misunderstanding here. "å'" is _not a valid character_ in a URL. It must be percent-escaped. Boost.URL only allows valid URLs to be parsed (otherwise it would be non-compliant with the RFC). If you want a particular character in the resulting URL you will need to assemble it using the modifier functions which accept plain strings, like this:  
  
```c++  
    boost::urls::url u;  
    u.params().append( { "limit", "100" } );  
    u.params().append( { "query",  
        "eq(RpOmrÃ¥de/arealplanid/planidentifikasjon,63280000)" } );  
```  
  
alternatively, you can write:  
  
```c++  
    boost::urls::url u;  
    u.set_query(   
        "?limit=100&query=eq(RpOmrÃ¥de/arealplanid/planidentifikasjon,63280000)" );  
```  
  
When you set part of a URL using a plain string, the library will automatically apply percent-encoding for you as needed.

---

## Comment 4

> Username: Dangeroustuber  
> Created at: 2023-02-13 13:42:40 UTC  
> Updated at: 2023-02-13 13:51:12 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427963728  

> When you set part of a URL using a plain string, the library will automatically apply percent-encoding for you as needed.  
  
But in the example you already have it percent encoded? maybe i'm misunderstanding?  
  
Do i have to fix the string myself for all cases that i care about (such as 'æ' 'ø' and 'å')? postman does send it correctly but by the time it gets to the std::string it's like in the first post on this issue, which is not correct.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-02-13 13:50:32 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427973314  

> But in the example you already have it percent encoded?  
  
oops... sorry about that! I copied and pasted the wrong text :) I have edited the code in that reply to reflect the correct text.

---

## Comment 6

> Username: Dangeroustuber  
> Created at: 2023-02-13 13:53:44 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427977586  

Right thanks so much, so something like this would work if i understood you correctly:  
  
```  
auto newstr = convert_string_from_UTF8(query_parameters);  
  
boost::urls::url par;  
par.set_query(newstr);  
```  
  
If i give boost::url something with 'å' for example, it will "fix" that for me.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-02-13 13:56:59 UTC  
> Updated at: 2023-02-13 13:57:28 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1427981840  

or you could just write  
```  
u.set_query( query_parameters );  
```

---

## Comment 8

> Username: Dangeroustuber  
> Created at: 2023-02-13 14:19:24 UTC  
> Url: https://github.com/boostorg/url/issues/683#issuecomment-1428018213  

Yes, that is a good suggestion. Thanks for all the help, i'm closing the issue.
