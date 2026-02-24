# #814 - Will it run on intel sandyBridge mobile processor... [Closed]

> Username: coderboyisongithub  
> Created at: 2019-01-17 07:08:41 UTC  
> Updated at: 2019-01-28 19:34:18 UTC  
> Closed at: 2019-01-28 19:34:18 UTC  
> Url: https://github.com/boostorg/compute/issues/814  

I have intel core -i3 sandy bridge proc.With integerated GPU..I dont have external GPU embedded on mother board...So can I use this library even if i don't have Nvidia or AMD GPUs?

---

## Comment 1

> Username: jszuppe  
> Created at: 2019-01-17 07:35:13 UTC  
> Url: https://github.com/boostorg/compute/issues/814#issuecomment-455071061  

It should. I don't know if the performance would be great as, IIRC, we did not optimise for Intel's iGPUs.

---

## Comment 2

> Username: coderboyisongithub  
> Created at: 2019-01-25 08:45:07 UTC  
> Url: https://github.com/boostorg/compute/issues/814#issuecomment-457498638  

If i want to gather information from GPU memory , like values of shader variable running there with Opengl ...  Can I do so?any possibility?

---

## Comment 3

> Username: keryell  
> Created at: 2019-01-26 01:50:58 UTC  
> Url: https://github.com/boostorg/compute/issues/814#issuecomment-457790402  

You will probably need to use the OpenGL interoperability mode of OpenCL provided by Boost.Compute...  
Boost.Compute is a library to ease OpenCL usage. So you "just" need to write the OpenCL kernel you want on your GPU.

---

## Comment 4

> Username: coderboyisongithub  
> Created at: 2019-01-26 13:20:40 UTC  
> Url: https://github.com/boostorg/compute/issues/814#issuecomment-457830580  

!! I have to write the whole kernel down?  
  
  
Sent from Mail for Windows 10  
  
From: Ronan Keryell  
Sent: 26 January 2019 07:36  
To: boostorg/compute  
Cc: coderboyisongithub; Author  
Subject: Re: [boostorg/compute] Will it run on intel sandyBridge mobileprocessor... (#814)  
  
You will probably need to use the OpenGL interoperability mode of OpenCL provided by Boost.Compute...  
Boost.Compute is a library to ease OpenCL usage. So you "just" need to write the OpenCL kernel you want on your GPU.  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 5

> Username: keryell  
> Created at: 2019-01-26 20:42:45 UTC  
> Url: https://github.com/boostorg/compute/issues/814#issuecomment-457864419  

Boost libraries are about C++ programming.  
But I agree that it would be nice to have a telepathy mode where you just think at your problem and then the solution is implemented... :-)
