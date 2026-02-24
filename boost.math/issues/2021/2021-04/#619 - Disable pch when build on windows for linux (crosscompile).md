# #619 - Disable pch when build on windows for linux (crosscompile) [Open]

> Username: slyshykO  
> Created at: 2021-04-29 09:19:27 UTC  
> Updated at: 2021-04-29 15:40:27 UTC  
> Url: https://github.com/boostorg/math/issues/619  

Since none variant of cross-tool knew by me support builds with pch, please, disable it.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-04-29 12:37:52 UTC  
> Url: https://github.com/boostorg/math/issues/619#issuecomment-829202916  

I don't understand this. There is no obligation to use precompiled headers from our end.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-04-29 15:25:26 UTC  
> Url: https://github.com/boostorg/math/issues/619#issuecomment-829333938  

@NAThompson by default the Jamfile that builds the Math TR1 library uses PCH's.  
  
It can be disabled by building with `b2 pch=off`.  
  
On a practical note, I'm not sure that it's possible for us detect this use case within the Jamfile and adjust pch setting accordingly, and also, does @slyshykO really need to build this library anyway?  It's only necessary if you're using the C language interfaces.

---

## Comment 3

> Username: slyshykO  
> Created at: 2021-04-29 15:40:27 UTC  
> Url: https://github.com/boostorg/math/issues/619#issuecomment-829346426  

I have encountered this problem when "build" boost through vcpkg.  
At this moment, I bypassed the problem by patching Jamfile.  
I will ask guys from vcpkg how to pass additional parameters to b2.  
  
Do b2 provide information about the host system?
