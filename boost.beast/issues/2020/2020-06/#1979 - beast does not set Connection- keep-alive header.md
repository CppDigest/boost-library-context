# #1979 - beast does not set Connection: keep-alive header. [Closed]

> Username: ashjas  
> Created at: 2020-06-10 08:40:01 UTC  
> Updated at: 2020-06-10 21:02:10 UTC  
> Closed at: 2020-06-10 10:08:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1979  

When i set the request's Connection header to keep-alive, and inspect the http packets, i dont see it set in the http header.  
i set it as:  
```req_.set(http::field::connection,		"keep-alive");```  
  
I also tried with ```req_.keep_alive(true);```  
but still cant see the header being set.  
  
I am looking for this header:  
```Connection: keep-alive```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-10 09:16:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-641868214  

I need more information.  
  
Did you set the HTTP version to 1.1 prior to setting keep-alive?  
  
Did you call prepare() on the message before sending it?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-06-10 10:08:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-641900483  

The behaviour you see is intended.  
  
In HTTP/1.1 and above, keep-alive behaviour is the default unless the Connection header includes the word "close".  
  
Beast follows the HTTP RFCs exactly. If your HTTP version is >= 11 (1.1) and you call keep_alive(true), Connection headers containing the word close are simply deleted.  
  
HTTP1.1 is the default in Beast, therefore keep-alive is also the default behaviour.

---

## Comment 3

> Username: ashjas  
> Created at: 2020-06-10 13:41:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642017050  

@madmongo1   
> Did you set the HTTP version to 1.1 prior to setting keep-alive?  
  
I dont set HTTP version. So requests use beast default which is as you pointed out HTTP 1.1  
  
>Did you call prepare() on the message before sending it?  
  
I dont find any ```req.prepare()``` method on boost 1.71. But i do set ```req.prepare_payload()```.  
are you referring to ```prepare_payload()``` ?  
  
So  
- I get that Beast default behaviour is keep-alive.  
- I understand that if ```req.keep_alive(true)``` is set, ```Connection: close``` CAN be discarded by beast.  
- But why is ```Connection: keep-alive``` discarded if i specifically set it? it does not matter if req.keep_alive(true) is set or not, this header is simply discarded in the http packet.  
  
>Beast follows the HTTP RFCs exactly. If your HTTP version is >= 11 (1.1) and you call keep_alive(true), Connection headers containing the word close are simply deleted.  
  
I dont observe this behaviour. Even if i dont set ```keep_alive(true)``` the ```Connection: close``` is discarded in the http packet.  
  
To summarize, no matter what keep_alive is set to.. the Connection header is not seen in the http packet.  
I am using boost v1.71

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-10 15:20:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642079454  

If you want "Connection: close" then call `keep_alive(false)`.

---

## Comment 5

> Username: ashjas  
> Created at: 2020-06-10 17:03:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642139744  

>If you want "Connection: close" then call keep_alive(false).  
  
With this too, the behaviour remains unchanged.  
as i mentioned:  
>To summarize, no matter what keep_alive is set to.. the Connection header is not seen in the http packet.  
  
To re-iterate my problem:  
I am a client that talks to a server.  
I want to indicate my intentions if i want the connection to be "keep-alive" or "close".  
If the server does not adhere to keep-alive connection then thats a different story.  
But if in my request i cant setup the "Connection: close/keep-alive" header through beast, how would the client convey its intentions?  
  
Summarizing: no matter what state the keep_alive value is set, and  along with setting http::field::connection to close/keep-alive, there is no Connection header in http requests when sniffed through wireshark.  
I am mailing you the code snippet that i am using.  
Thanks.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-06-10 18:16:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642177777  

Yes this has been explained. HTTP/1.1 with no Connection header expresses the intent "keep alive." HTTP/1.0 with no Connection header expresses "close."

---

## Comment 7

> Username: ashjas  
> Created at: 2020-06-10 20:23:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642237551  

Ok, so this is what i understood.  
  
- In HTTP1.1 the Connection: close/keep-alive seems to be NOT mandatory/deprecated.  
- By default, on HTTP1.1 the server will maintain the connections untill the client disconnects.  
- Connection: close at client side is emulated by just disconnecting at end of getting the response,the server does not need to know this by means of Connection: header.  
- If server talking on HTTP1.1 decides to close the connection after sending the response, it will send the Connection: close in the response header.  
- So on HTTP1.1 to achieve this, Beast removes the Connection: header no matter what http req packet was set with, even if explicitly said to add a connection header ````req_.set(http::field::connection, "keep-alive");```` or ````req_.set(http::field::connection, "close");````  
  
Have i got it all right?  
  
Thanks.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-06-10 21:02:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1979#issuecomment-642258270  

> Have i got it all right?  
  
Hell yes! You got it perfect! Maybe you should help with writing the documentation :)
