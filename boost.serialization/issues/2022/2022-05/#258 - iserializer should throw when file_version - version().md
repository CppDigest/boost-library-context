# #258 - iserializer should throw when file_version > version() [Closed]

> Username: paulharris  
> Created at: 2022-05-22 01:11:08 UTC  
> Updated at: 2023-02-02 01:47:38 UTC  
> Closed at: 2022-09-15 22:35:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/258  

https://github.com/boostorg/serialization/blob/b7ae64e95c74f6536f268e9ed2fe0f8d1fa8475e/include/boost/archive/detail/iserializer.hpp#L177  
  
The comment in the code is that "To permit readers of these old archives, we have to suppress this code"  
The code was a check that would throw if the file_version was greater than the class version - ie the serializer would not be able to correctly parse the serialized data.  
  
Surely everyone wants the code to correctly detect and throw errors when trying to load data that is incompatible?  
Currently the code can crash while parsing slightly incompatible files (especially binary archives).  
  
The comment went further to say "Perhaps in the future we might re-enable it but permit its suppression with a runtime switch"  
  
Is this possible now?   I have had to patch this one line of code for years now, and I'd like to be able to use boost unpatched.  
  
Since this switch has been off for so long, perhaps just change it to a compile-time switch?

---

## Comment 1

> Username: paulharris  
> Created at: 2022-09-15 22:37:02 UTC  
> Url: https://github.com/boostorg/serialization/issues/258#issuecomment-1248710551  

Thank you!

---

## Comment 2

> Username: XDU-fish  
> Created at: 2023-02-01 06:37:44 UTC  
> Updated at: 2023-02-02 01:47:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/258#issuecomment-1411539318  

Hi, I noticed that the commented code had been opend in the develop branch before the 1.81 release.  Why it is still being commented in boost 1.81 release?  Could this code be uncommented in boost 1.82 version?
