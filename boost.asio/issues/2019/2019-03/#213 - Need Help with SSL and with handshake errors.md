# #213 - Need Help with SSL and with handshake errors [Closed]

> Username: DragonOsman  
> Created at: 2019-03-08 18:43:43 UTC  
> Updated at: 2019-03-09 11:29:56 UTC  
> Closed at: 2019-03-09 11:29:56 UTC  
> Url: https://github.com/boostorg/asio/issues/213  

I have a problem with using SSL to switch from HTTP to HTTPS in a web server app I have.  The code is [here](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter.cpp) and my server_certificate.hpp file is [here](https://github.com/DragonOsman/currency_converter/blob/master/server_certificate.hpp). The app uses Boost 1.69.0, for Boost.Beast.  I took synchronous HTTP SSL server example code and synchronous HTTP example client code from the Boost.Beast repository on here.    
  
I got the certificate from Let's Encrypt using the TXT record method, and it's a domain-specific certificate.  I was told by the guys at Let's Encrypt forums that it doesn't require DH parameters, but I'd like to confirm here if it's true or not.  I didn't get DH parameters.  It's a certificate issued via acme.sh.  The domain name is a subdomain I got for free from dynu.com; the .cer files I have are ca.cer, {DOMAIN}.cer, and fullchain.cer.  I was told that I'd need fullchain.cer so I put that in as the value for the `cert` string (it consists of two certificates and I used both).  The private key I have is in a .key file rather than a .pem file and I'm wondering if that's a problem.  I also need to ask what I need to do with the ctx.set_options and ctx.set_password_callback methods.    
  
Could you please help me (if this is good to ask here)?  When I try to visit my app in the browser while my server is running (I'm hosting it on my computer), I get this error in my console window:  
```  
handshake: The file handle supplied is not valid  
```  
And before, I also got this error:  
```  
handshake: An operation was attempted on something that is not a socket  
```  
And I also get an error page in my browser saying, "Cannot connect securely to this page".  I'm using the MS Edge browser.

---

## Comment 1

> Username: DragonOsman  
> Created at: 2019-03-09 11:29:56 UTC  
> Url: https://github.com/boostorg/asio/issues/213#issuecomment-471169231  

I'm closing this because I was told to open the issue [here](http://github.com/chriskohlhoff/asio), which I did.  I also sent an email about it to the Boost Users mailing list (just in case).
