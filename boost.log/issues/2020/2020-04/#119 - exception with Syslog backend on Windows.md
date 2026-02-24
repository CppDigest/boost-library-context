# #119 - exception with Syslog backend on Windows [Closed]

> Username: zaphod71  
> Created at: 2020-04-27 14:44:20 UTC  
> Updated at: 2020-04-27 18:53:20 UTC  
> Closed at: 2020-04-27 15:47:15 UTC  
> Url: https://github.com/boostorg/log/issues/119  

moved from boot 1.68.0 to 1.72 and newer. The logging to Syslog backerd on Windows platform causes an exception on new boost version (both toolsets). With 1.68.0 running without any issue.  
  
Library is build with VS 2019 with different toolsets 1.40 and 1.41 with this build commands:  
  
.\b2 --build-dir=c:\temp\build --stagedir=c:\temp\stage link=static --build-type=complete --toolset=msvc-14.0 stage --with-regex --with-filesystem --with-program_options --with-log   
  
and  
  
.\b2 --build-dir=c:\temp\build --stagedir=c:\temp\stage link=static --build-type=complete --toolset=msvc-14.2 stage --with-regex --with-filesystem --with-program_options --with-log   
  
  
  
  
[boostlog_test.zip](https://github.com/boostorg/log/files/4540283/boostlog_test.zip)  
  
![screenshot_20200427_164330](https://user-images.githubusercontent.com/64422359/80385383-4910ae00-88a6-11ea-93f2-5d5624a52efb.png)

---

## Comment 1

> Username: Lastique  
> Created at: 2020-04-27 15:47:15 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620069209  

In my case I get an exception with `WSAHOST_NOT_FOUND` error code from Boost.ASIO. Indeed, I don't have the have a host named `marvin`.  
  
You should catch the exception and check the error code you're getting.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-04-27 15:49:06 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620070309  

Also, note that you're not adding a MyLevel attribute anywhere. The attribute is used in the severity mapping function you're setting. This is not what is causing the exception, but a separate problem.

---

## Comment 3

> Username: zaphod71  
> Created at: 2020-04-27 16:02:00 UTC  
> Updated at: 2020-04-27 16:02:25 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620078431  

Of course, the host in in my network and with boost 1.68 this demo example is working. I copied the code from boost documentation:  
https://www.boost.org/doc/libs/1_73_0_beta1/libs/log/doc/html/log/detailed/sink_backends.html#log.detailed.sink_backends.syslog

---

## Comment 4

> Username: Lastique  
> Created at: 2020-04-27 16:04:40 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620080058  

In that case I can only recommend you to debug the issue. What error code are you getting? What is the exception backtrace?

---

## Comment 5

> Username: zaphod71  
> Created at: 2020-04-27 16:15:58 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620086424  

It is a WINSOCK error:  
WSAEAFNOSUPPORT (10047) Address family not supported by protocol family  
  
But what changed from 1.68 to 1.72?

---

## Comment 6

> Username: Lastique  
> Created at: 2020-04-27 16:30:42 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620094584  

Obviously, most libraries have changed. In this case, I believe, Boost.ASIO is the most relevant library.  
  
I suspect, the error you're getting could be caused by mismatch of IP versions of the resolved address of the `marvin` host and the local address from which syslog messages are being sent. You're not setting any local address, so any local IPv4 interface is being used. Which means the `marvin` hostname resolving result has an IPv6 as the first entry.  
  
I'm not sure what changed in Boost.ASIO or elsewhere. But you can fix this by explicitly setting local and target addresses with matching IP versions.

---

## Comment 7

> Username: zaphod71  
> Created at: 2020-04-27 18:13:59 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620148080  

Yes,  
it seems that the host name is resolved in a different way now. If the ip address is specified it works.

---

## Comment 8

> Username: Lastique  
> Created at: 2020-04-27 18:53:20 UTC  
> Url: https://github.com/boostorg/log/issues/119#issuecomment-620168438  

I have added a fix that should restrict name resolution results to only the addresses with the IP version matching that which was specified on sink backend construction. You could try if that makes your original example work.
