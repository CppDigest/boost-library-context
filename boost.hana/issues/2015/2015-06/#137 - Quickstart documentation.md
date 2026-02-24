# #137 - Quickstart documentation [Closed]

> Username: camio  
> Created at: 2015-06-17 19:00:38 UTC  
> Updated at: 2015-06-19 01:40:51 UTC  
> Closed at: 2015-06-19 01:40:51 UTC  
> Url: https://github.com/boostorg/hana/issues/137  

missing '#include <string>' which is required for members of Fish, Cat, Dog, etc.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-18 12:17:51 UTC  
> Url: https://github.com/boostorg/hana/issues/137#issuecomment-113135208  

Do you mean `#include <string>`? I can't say "first, let's include the library" and include `<string>` too, and it would feel like making the reader lose its time to specify "hey, don't forget to `#include <string>`".

---

## Comment 2

> Username: camio  
> Created at: 2015-06-18 20:27:32 UTC  
> Updated at: 2015-06-18 20:28:21 UTC  
> Url: https://github.com/boostorg/hana/issues/137#issuecomment-113279989  

Yes, I meant #include<string>.  
  
When I went through the example in the docs it was a bit awkward when I was trying to reproduce the code in my own file by copy-paste. I got a compilation error and then realized I needed some more includes. I imagine most people going through the quick start in that way will run into the same issue.  
  
Also, related to this is that "using namespace boost::hana::literals" isn't in the documentation but is required for compilation of part of the examples. I ran into that as well and actually didn't figure out how to fix it until I looked into quickstart.cpp directly.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-19 01:40:51 UTC  
> Url: https://github.com/boostorg/hana/issues/137#issuecomment-113337333  

Fixed. The tutorial will be updated when I push to `master`, after the review. Thanks for the report.
