# #2103 - Crashing in engine.ipp [Closed]

> Username: Raj123456788  
> Created at: 2020-10-14 23:16:31 UTC  
> Updated at: 2020-10-26 16:24:38 UTC  
> Closed at: 2020-10-26 16:24:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2103  

Don't get any info but this:  
<img width="1177" alt="Screen Shot 2020-10-14 at 4 13 00 PM" src="https://user-images.githubusercontent.com/16295521/96055202-4a279e80-0e38-11eb-94ec-065f93779367.png">  
  
Let me know if anything is wrong with my string data.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-15 06:37:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2103#issuecomment-708933339  

This kind of thing is normally because the object containing your ssl stream has been deleted.

---

## Comment 2

> Username: Raj123456788  
> Created at: 2020-10-15 19:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2103#issuecomment-709554751  

@madmongo1 : Thanks.  I have below code   
                            res1.body() = data;  
                            res1.prepare_payload();  
                            res1.keep_alive(req.keep_alive());  
                            return (send(std::move(res1)));  
By the time the data is sent over the ssl res1 changes. is there a way I can handle this since data is coming at a fast pace.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-10-15 20:39:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2103#issuecomment-709577842  

You could use the async_ interface as per the examples?  
https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/examples.html#beast.examples.servers

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-10-26 16:24:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2103#issuecomment-716663193  

Activity on this issue seems to have trailed off so I'll close it for now. If you're still having problems, by all means open another issue. It's helpful if you can post a small, complete program on github that demonstrates the issue so I can debug it locally.
