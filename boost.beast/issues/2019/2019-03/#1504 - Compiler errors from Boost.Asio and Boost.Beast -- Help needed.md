# #1504 - Compiler errors from Boost.Asio and Boost.Beast -- Help needed [Closed]

> Username: DragonOsman  
> Created at: 2019-03-06 15:16:45 UTC  
> Updated at: 2019-03-09 11:32:42 UTC  
> Closed at: 2019-03-09 11:32:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1504  

### Version of Beast  
Version 189 for the one in Boost distribution, and 228 for the one cloned from the GitHub repository (I'm using the former one, but I needed the latter as well for <boost::beast::ssl.hpp>).    
  
### Steps necessary to reproduce the problem  
As the title says, I have errors that keep it from compiling and that's my problem.  The code is [here](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter.cpp) and my server_certificate.hpp file is [here](https://github.com/DragonOsman/currency_converter/blob/master/server_certificate.hpp).   
  
I got the certificate from Let's Encrypt using the TXT record method, and it's a domain-specific certificate.  I was told by the guys at Let's Encrypt forums that it doesn't require DH parameters, but I'd like to confirm here if it's true or not.  I didn't get DH parameters.  It's a certificate issued via acme.sh.  The domain name is a subdomain I got for free from dynu.com; the .cer files I have are ca.cer, {DOMAIN}.cer, and fullchain.cer.  I was told that I'd need fullchain.cer so I put that in as the value for the `cert` string (it consists of two certificates and I used both).  The private key I have is in a .key file rather than a .pem file and I'm wondering if that's a problem.  I also need to ask what I need to do with the ctx.set_options and ctx.set_password_callback methods.    
  
  
### All relevant compiler information  
This [Gist](https://gist.github.com/DragonOsman/53e95fb172aaf6e310d889ca65220aa9) includes the entire compiler output I got.  I'm using Visual Studio Community 2017 with the latest update and I have the MSBuild diagnostics level set to "classic" which gives you information about the whole development environment in addition to errors, warnings and line numbers.  So it took more time to complete the build attempt and the resulting output message is also a lot longer.  Sorry about that.    
  
Thanks in advance for any replies and/or help.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-06 15:18:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1504#issuecomment-470146340  

You can't use Boost 1.69 with Beast 228 - you have to use the same version of boost and beast.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2019-03-06 18:18:34 UTC  
> Updated at: 2019-03-06 19:26:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1504#issuecomment-470217176  

Thanks for the reply.  There were still errors and I can't choose to include only Asio from the Boost 1.69.0 (in the project's include directory setting in the IDE), so I had to completely give up on HTTPS (again).  I wanted to do it this time because I needed both my portfolio website (hosted with HTTPS on GitHub Pages) and the web server app to both be on HTTPS to avoid conflicts.  Otherwise I couldn't make a request to the web server app from my portfolio site to see if the web server app is running.  I'll just have to manually change the note for the web server app in my work.html page to say if it's running or not, now.    
  
Anyway, right now it's running and you can find it at this URL: http://dragonosman.dynu.net:5501/ .  It uses a Google Map GUI with geolocation to open an info window with a currency conversion form at the user's location with the "to" currency in the form set to the one used at that place.  If geolocation isn't working, like if either the user didn't give permission or the browser doesn't support geolocation to begin with, the user will see an error but could just click anywhere on the map to get the info window with the HTML form on it and the "to" currency dropdown will be set to the one used at that location.  There are exceptions, though, since there are places that don't work--note on info window tells you which ones they are (the note doesn't come up when the error about geolocation not working is displayed--I should probably change that).    
  
Sorry about the shameless advertising.  I wanted to say it since the app uses Beast server and client example code.  There's another little note I need to give here, though: the dropdown menu for the base currency is set to USD by default and also disabled.  The reason for this is that the currency API I'm using doesn't allow changing the base currency and it's USD by default.  
  
Edit: It should work if I use standalone Asio, even if I use a different version from the Beast version.  So I'll try that and see.

---

## Comment 3

> Username: DragonOsman  
> Created at: 2019-03-06 19:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1504#issuecomment-470254219  

I've decided to use the latest version, each, of the Boost.Asio and Boost.Beast from their respective GitHub repositories.  That way there should be problems aside from SSL certificates.  
  
I'll ask back if I still have problems regarding the SSL certificates.

---

## Comment 4

> Username: DragonOsman  
> Created at: 2019-03-08 18:24:55 UTC  
> Updated at: 2019-03-08 18:33:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1504#issuecomment-471027346  

@vinniefalco I got it to build successfully.  But I still can't connect securely to the web app in my browser (it doesn't give me the warning that I might be being fooled about the site using HTTPS--it's a different message and it appears on the webpage itself).  You can see my .cpp file [here](https://github.com/DragonOsman/currency_converter/blob/master/currency_converter.cpp); the server_certificate.hpp file is the same as before (from when I linked to it here).    
  
Also, when I tried to visit the app in my browser, I got these messages in the console window:  
```  
handshake: The file handle supplied is not valid  
handshake: The file handle supplied is not valid  
handshake: An operation was attempted on something that is not a socket  
handshake: The file handle supplied is not valid  
handshake: The file handle supplied is not valid  
handshake: An operation was attempted on something that is not a socket  
```  
What did I do wrong?  And is this something I should be asking at the Boost.Asio repository or is here fine too?  
  
By the way, there were some linker errors I was getting because of OpenSSL libs that I was able to solve by adding this:  
```cpp  
#ifdef WIN32  
#pragma comment (lib, "crypt32.lib")  
#endif  
```  
Just adding it here for reference.  
  
I still want to know about what I asked here before about DH parameters and the other stuff I asked about concerning my certificate and how I should use it compared to what I currently have.  Thanks in advance.

---

## Comment 5

> Username: DragonOsman  
> Created at: 2019-03-09 11:32:30 UTC  
> Updated at: 2019-03-09 11:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1504#issuecomment-471169393  

Okay, I've opened the issue [here](https://github.com/chriskohlhoff/asio) and also sent an email about the problem to the Boost Users mailing list (just in case), so I'm closing this issue.  Sorry about this and also for bothering you guys.
