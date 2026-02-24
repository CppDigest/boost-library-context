# #963 - HTTP: async client and browser behavior for ssl examples [Closed]

> Username: TurtleWithWheels156  
> Created at: 2018-01-02 06:10:48 UTC  
> Updated at: 2018-01-03 23:07:24 UTC  
> Closed at: 2018-01-03 23:07:24 UTC  
> Url: https://github.com/boostorg/beast/issues/963  

Hi Vinnie,  
  
I'm new to beast and am looking through the different examples; I appreciate the time that went into it and had a question regarding the async server/client examples that use ssl.  I ran the server and tested a request for an html file with the client and got a response with the header and body; it also looks like the server prints out "shutdown: End of file".  However, going through a browser (e.g. Chrome) and trying to request the same page responds with "... didn’t send any data." with the server printing out "handshake: http request".    
  
It's not a bug or an issue, rather I am currently getting a grasp of the code and was wondering if you knew what might be causing this behavior, thanks.

---

## Comment 1

> Username: djarek  
> Created at: 2018-01-02 09:36:07 UTC  
> Url: https://github.com/boostorg/beast/issues/963#issuecomment-354729270  

First guess should in this case be that the browser might not like your self-signed certificate.

---

## Comment 2

> Username: TurtleWithWheels156  
> Created at: 2018-01-03 23:07:24 UTC  
> Url: https://github.com/boostorg/beast/issues/963#issuecomment-355153508  

Oh okay I'll look into that.  Thanks.
