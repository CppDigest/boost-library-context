# #1049 - body request send wrong data [Closed]

> Username: Ludea  
> Created at: 2018-02-28 13:29:44 UTC  
> Updated at: 2018-02-28 13:53:19 UTC  
> Closed at: 2018-02-28 13:53:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1049  

### Version of Beast  
144  
  
### Steps necessary to reproduce the problem  
Hello, I have a passport application [.](http://www.passportjs.org/) with passport local strategie.  
I can login with curl like this :  
`curl -X POST -v --data "username=myuser&password=mypass" myhostname`   
  
I try to reproduced that http request with Boost Beast.  
I start with ssl example. But when I start the http request, I get 500 internal server error.  
Here the changes from example:  
```  
    // Set up an HTTP POST request message  
    req.version(version);  
    req.method(http::verb::post);  
    req.target(target);  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req.set(http::field::content_type, "application/x-www-form-urlencoded");  
    req.body() = "username=myuser&password=mypass" ;  
```  
  
I don't understand what is wrong.  
I try with `text/plain`, but same result.  
  
Thanks for help  
(The get method works)  
### All relevant compiler information  
  
I use GCC 5.3

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-28 13:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1049#issuecomment-369243999  

Well, first of all a server should not get an internal error on a bad request. Second, you forgot to include the Content-Length field. Try adding this line:  
```  
req.prepare_payload();  
```  
after assigning the body, to set the Content-Length field.

---

## Comment 2

> Username: Ludea  
> Created at: 2018-02-28 13:53:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1049#issuecomment-369245497  

Yes, I forgot this function -_-  
Now it's working.  
Thanks for help :)
