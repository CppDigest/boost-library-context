# #876 - is a very long unzip (45-90 mins) time on windows expected? [Open]

> Username: anandijain  
> Created at: 2024-03-29 17:16:21 UTC  
> Updated at: 2025-01-30 09:28:09 UTC  
> Url: https://github.com/boostorg/boost/issues/876  

I don't know much about cpp development, and was just looking for a simple way to do serial communication to an arduino in cpp.  
   
I was lead to `boost/asio.hpp` but downloading and unzipping the release (both 1.84 and 1.82) is estimated at 90 minutes.   
  
Is this a normal amount of time or am I doing something wrong? Can I speed it up somehow?  
  
Thanks

---

## Comment 1

> Username: mclow  
> Created at: 2024-03-29 17:21:41 UTC  
> Url: https://github.com/boostorg/boost/issues/876#issuecomment-2027517876  

Where are you downloading it from?  
  
I just downloaded 1.84 from https://boostorg.jfrog.io/artifactory/main/release/1.84.0/source/boost_1_84_0_rc1.tar.gz, and it took about 8 seconds.  Unzipping took a bit longer, but under 20 seconds.

---

## Comment 2

> Username: anandijain  
> Created at: 2024-03-29 19:00:30 UTC  
> Url: https://github.com/boostorg/boost/issues/876#issuecomment-2027620723  

I got it from the github releases page. downloading was fast but unzipping was slow. I'll try that link in a bit

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-03-29 19:12:55 UTC  
> Updated at: 2024-03-29 19:19:08 UTC  
> Url: https://github.com/boostorg/boost/issues/876#issuecomment-2027642218  

@anandijain   
  
How to Write a Good Bug Report [Step-By-Step Guide]  https://marker.io/blog/how-to-write-bug-report  
  
---   
  
Edit:  You should think "what questions might they ask me?"  "how can this be replicated"?  
  
 Such as the exact link you downloaded from, the exact command you used to unzip the archive, your geographical location for the download, information about your local operating system etc etc.

---

## Comment 4

> Username: gast128  
> Created at: 2025-01-30 09:28:09 UTC  
> Url: https://github.com/boostorg/boost/issues/876#issuecomment-2623958251  

Guessing here. The Windows explorer can take ages to unzip large compressed archives.
