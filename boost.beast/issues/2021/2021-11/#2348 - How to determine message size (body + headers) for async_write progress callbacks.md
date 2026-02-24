# #2348 - How to determine message size (body + headers) for async_write progress callbacks [Closed]

> Username: amirfi95  
> Created at: 2021-11-22 15:09:16 UTC  
> Updated at: 2021-11-22 16:59:12 UTC  
> Closed at: 2021-11-22 16:59:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2348  

I need progress callbacks for my http client.  
Implementing the read callback was quite straightforward.  
  
But I am struggling to implement a write callback because I am not able to determine the size in bytes of my http_request.  
  
The read callback has been implemented by seperating the reading of the header from the body.  
I am then counting the tranfsered_bytes of the body (async_read_some) while using the content_length of the response to get the size of the payload.  
  
Buuuut... How do I get the size of my complete http_request in bytes ?  
Am I supposed to seperate the write as well ?   
There is no need for me to explicitly write the header and then the body  and it would unnecessary complicate the write part.  
  
  
Is there a possibility to avoid sending the header seperatly in order to count the transfered bytes of the body ?  
  
  
Am I missing something :) ?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-11-22 15:46:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2348#issuecomment-975659040  

Calling `prepare_payload` on the request object will have the following effect:  
> This function will adjust the Content-Length and Transfer-Encoding field values based on the properties of the body.  
  
Implementing the write in terms of `http::async_write_header` and `http::async_write_some`, as you have done with the read should then give you what you need.  
  
That's probably the simplest option at this stage.

---

## Comment 2

> Username: amirfi95  
> Created at: 2021-11-22 16:59:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2348#issuecomment-975729749  

thanks :)   
Okay cool, that's not really a problem.  
I thought I was simply missing some kind of size() function for the whole message.
