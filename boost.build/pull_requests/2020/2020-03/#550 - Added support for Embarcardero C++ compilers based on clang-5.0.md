# #550 Added support for Embarcardero C++ compilers based on clang-5.0 [Closed]

> Username: eldiener  
> Created at: 2020-03-21 00:51:56 UTC  
> Updated at: 2021-10-02 21:13:26 UTC  
> Closed at: 2020-03-21 00:56:25 UTC  
> Url: https://github.com/boostorg/build/pull/550  

Embarcadero has new clang-5.0 based compilers which are much more C++ compliant than  
the long-standing bcc32 Borland compiler. These compiler on Windows are:  
  
bcc32c - a 32-bit clang-based compiler whose command flags are the same as bcc32.  
bcc32x - a 32-bit clang-based compiler whose command flags are largely compliant with clang.  
bcc64  - a 64-bit clang-based compiler whose command flags are largely compliant with clang.  
  
The bcc32c and bcc32x compilers are exactly the same under the covers, but just take  
a different set of command flags..  
  
I have upgraded borland.jam to support bcc32c while upgrading the suppport for  
bcc32 to the latest versions of C++ Builder.  
  
I have created an embarcadero.jam toolset for the bcc32x and bcc64 compilers.  
  
I have tested my changes pretty thoroughly, using C++Builder Community Edition  
which you can get for free at https://www.embarcadero.com/products/cbuilder/starter.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-03-21 00:56:57 UTC  
> Url: https://github.com/boostorg/build/pull/550#issuecomment-601969392  

I am going to resubmit without all the extraneaous changes I have picked up somehow.

---
