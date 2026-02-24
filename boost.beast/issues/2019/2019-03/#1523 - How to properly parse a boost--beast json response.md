# #1523 - How to properly parse a boost::beast json response? [Closed]

> Username: cylussec  
> Created at: 2019-03-17 22:19:44 UTC  
> Updated at: 2019-03-17 23:44:26 UTC  
> Closed at: 2019-03-17 23:44:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1523  

I have been working with Beast as an HTTP client. I am able to get the json response into the body as a dynamic_body type. However, I feel like I am missing something with the next step as I try to get that data into custom objects I have. I can convert it to a string with buffers_to_string, and then parse it, but it feels like I am missing the point of the dynamic_body (or the body type in general).   
  
Whats the best way to parse through a json structured body response?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-17 22:21:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1523#issuecomment-473720944  

That depends on what your JSON parser requires as input. If it can parse incrementally, you can feed it each buffer in the dynamic body one at a time using a loop. If your JSON parser requires the entire JSON object to be submitted all at once, in a single buffer, you should be using the `basic_dynamic_body<flat_buffer>` type instead of `dynamic_body`.  
  
Which JSON parser are you using?

---

## Comment 2

> Username: cylussec  
> Created at: 2019-03-17 23:13:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1523#issuecomment-473725338  

I hadn't gone down that route because it seemed like I might be doing something wrong (some of your comments in other threads made it seem like there might be some kind of native ability to iterate over the response body). If that's not the case, then that also answers my question!  
  
Thanks for the clarification.
