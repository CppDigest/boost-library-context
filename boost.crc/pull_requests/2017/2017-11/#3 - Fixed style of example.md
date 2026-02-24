# #3 Fixed style of example [Open]

> Username: marcusmueller  
> Created at: 2017-11-01 12:28:22 UTC  
> Updated at: 2018-04-09 10:17:08 UTC  
> Url: https://github.com/boostorg/crc/pull/3  

While it's certainly nice that C++ allows you to not have {} to  
encapsulate a function body if that body only consists of a single  
statement (here: `try{…}`), it is not of any familiarity to C++ coders  
to omit them.  
  
Same goes for putting `main`'s argc/argv arguments on multiple lines.  
  
Also, defining a macro PRIVATE_SOMETHING just to use it once one line of  
code below is a bit of a strange thing to do.  
  
So, I got rid of the macro and applied the clang-format style from  
https://github.com/boost-experimental/vc

---

## Comment 1

> Username: CTMacUser  
> Created_at: 2018-04-09 09:05:08 UTC  
> Url: https://github.com/boostorg/crc/pull/3#issuecomment-379684983  

I felt if everything was going to be `try`-ed, then there was no point implying a structure that would allow me to insert statements around the `try` block in the future.  
  
And teaching the novices about function-whole `try` blocks is a good thing!  (OK, I'm not teaching it, but making novices look it up in their C++ book, [or](https://stackoverflow.com/q/3633419/1010226) [on](https://stackoverflow.com/q/9407376/1010226) [StackOverflow](https://stackoverflow.com/q/5612486/1010226), is still good.)

---

## Comment 2

> Username: marcusmueller  
> Created_at: 2018-04-09 10:12:57 UTC  
> Url: https://github.com/boostorg/crc/pull/3#issuecomment-379703405  

I beg to differ. This is an example meant to be easy to understand; it's not meant to teach C++ syntax that people seldom come across.  
  
(I'd give my opinion about the quality of the idea of doing single statement body blocks without brackets, but I already did so.)

---

## Comment 3

> Username: marcusmueller  
> Created_at: 2018-04-09 10:17:08 UTC  
> Url: https://github.com/boostorg/crc/pull/3#issuecomment-379704461  

Also, boost at least strives to have a common and (compared to omission of brackets in function bodies) sane style; that's why the aforementioned style file exists. I don't think individual examples should choose to diverge from that.

---
