# #943 - How can you create the request body after reading the headers? [Closed]

> Username: chrisics  
> Created at: 2017-12-20 07:28:08 UTC  
> Updated at: 2017-12-21 07:31:05 UTC  
> Closed at: 2017-12-21 07:31:05 UTC  
> Url: https://github.com/boostorg/beast/issues/943  

Hello,  
  
We are retrofitting our REST server to use beast.   
In our application we need to create the type of the body (file, string, ...) after reading the http headers.  
Because some POST messages have a 50+ MB body and some only have a body of 200 bytes.  
  
How can I do this?    
Do I need a custom parser to go with async_read_header for this?  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-20 14:02:53 UTC  
> Url: https://github.com/boostorg/beast/issues/943#issuecomment-353070896  

Like this? http://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/more_examples/change_body_type.html

---

## Comment 2

> Username: chrisics  
> Created at: 2017-12-20 15:15:35 UTC  
> Url: https://github.com/boostorg/beast/issues/943#issuecomment-353089979  

Of course, I should have known it was in there...  
Thanks,  
Great library!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-12-20 17:55:46 UTC  
> Url: https://github.com/boostorg/beast/issues/943#issuecomment-353136269  

I appreciate the kind words. Any time you need help, please do not hesitate to reach out. If you want to communicate privately, my email address is on my Github profile and at the top of each beast source file (I keep all email communications confidential unless you instruct otherwise). I can provide assistance for any beast-related aspect including design work.
