# #55 - Documentation errors regarding SafeInt [Closed]

> Username: dcleblanc  
> Created at: 2018-03-26 22:38:37 UTC  
> Updated at: 2018-09-23 22:26:37 UTC  
> Closed at: 2018-08-10 21:05:58 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/55  

SafeInt isn't currently a Boost library, which is why it doesn't use Boost conventions. It doesn't have any dependency on Boost.  
  
SafeInt does not require porting for different compilers, is fully supported on gcc, clang, and Visual Studio, and has been since 2009, with improvements made in this area in 2014.  
  
The license has been changed from MS-PL to MIT license (very recent change).  
  
The library has had a test suite since before it was public, and is now located here:  
https://github.com/dcleblanc/SafeInt/Test  
  
SafeInt also has no external dependencies other than standard library files, and doesn't need anything else installed to work.  
  
I appreciate the attribution, but let's ensure that the documentation is correct, and current.

---

## Comment 1

> Username: robertramey  
> Created at: 2018-08-10 21:05:54 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/55#issuecomment-412206288  

Hmmm - I think "documentation errors" is a little snarky.  But never-the-less, I've added the information about to the documentation.

---

## Comment 2

> Username: dcleblanc  
> Created at: 2018-08-10 21:11:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/55#issuecomment-412207773  

Thanks!

---

## Comment 3

> Username: dcleblanc  
> Created at: 2018-08-10 21:19:44 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/55#issuecomment-412209538  

Wasn’t meant to be snarky, sorry if it was taken that way. Thanks for fixing these inaccurate comments.  
  
From: Robert Ramey <notifications@github.com>  
Sent: Friday, August 10, 2018 2:06 PM  
To: robertramey/safe_numerics <safe_numerics@noreply.github.com>  
Cc: David LeBlanc <dleblanc@exchange.microsoft.com>; Author <author@noreply.github.com>  
Subject: Re: [robertramey/safe_numerics] Documentation errors regarding SafeInt (#55)  
  
  
Hmmm - I think "documentation errors" is a little snarky. But never-the-less, I've added the information about to the documentation.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub<https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Frobertramey%2Fsafe_numerics%2Fissues%2F55%23issuecomment-412206288&data=02%7C01%7Cdleblanc%40exchange.microsoft.com%7C89a6faeeb63545fbd36808d5ff0510cf%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636695319585082285&sdata=HycZxi7Nl8q4PROTfnW5Pk7dcqArZjiMxB9ppviUmqk%3D&reserved=0>, or mute the thread<https://na01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fnotifications%2Funsubscribe-auth%2FAe_sLw53i72pRhWRuD9qqSxKgEXmznh2ks5uPfWzgaJpZM4S8A9v&data=02%7C01%7Cdleblanc%40exchange.microsoft.com%7C89a6faeeb63545fbd36808d5ff0510cf%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636695319585092289&sdata=Fr%2B0WTauwwMCrN%2BLOdOfsUb%2BWGerynBmEUxaPbGXnfM%3D&reserved=0>.
