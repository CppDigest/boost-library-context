# #551 Added support for Embarcardero C++ compilers based on clang-5.0 [Merged]

> Username: eldiener  
> Created at: 2020-03-21 01:34:48 UTC  
> Updated at: 2021-10-02 21:13:25 UTC  
> Merged at: 2020-03-21 02:33:17 UTC  
> Closed at: 2020-03-21 02:33:17 UTC  
> Url: https://github.com/boostorg/build/pull/551  

Embarcadero has new clang-5.0 based compilers which are much more C++ compliant than  
the long-standing bcc32 Borland compiler. These compiler on Windows are:  
  
bcc32c - a 32-bit clang-based compiler whose command flags are the same as bcc32.  
bcc32x - a 32-bit clang-based compiler whose command flags are largely compliant with clang.  
bcc64 - a 64-bit clang-based compiler whose command flags are largely compliant with clang.  
  
The bcc32c and bcc32x compilers are exactly the same under the covers, but just take  
a different set of command flags..  
  
I have upgraded borland.jam to support bcc32c while upgrading the suppport for  
bcc32 to the latest versions of C++ Builder.  
  
I have created an embarcadero.jam toolset for the bcc32x and bcc64 compilers.  
  
I have tested my changes pretty thoroughly, using C++Builder Community Edition  
which you can get for free at https://www.embarcadero.com/products/cbuilder/starter.  
  
Added support for Embarcardero C++ compilers based on clang-5.0

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-03-21 01:47:37 UTC  
> Url: https://github.com/boostorg/build/pull/551#issuecomment-601975813  

Thank you for this work... I tried for a few months last year to make a new toolset like this. But never had enough time to work out all the details (not very good docs for Embarcadero).

---
