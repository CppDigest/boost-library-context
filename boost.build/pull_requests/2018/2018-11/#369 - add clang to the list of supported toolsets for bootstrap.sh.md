# #369 add clang to the list of supported toolsets for bootstrap.sh [Merged]

> Username: jeking3  
> Created at: 2018-11-17 00:48:07 UTC  
> Updated at: 2021-10-02 21:19:27 UTC  
> Merged at: 2018-11-18 20:46:18 UTC  
> Closed at: 2018-11-18 20:46:18 UTC  
> Url: https://github.com/boostorg/build/pull/369  

clang was not in the list of supported toolsets - it was also missing from the Guess_Toolset script so I slotted it in under gcc.  This means when guessing we will prefer gcc, then clang.  It is better than missing clang all together and being unable to guess.  
  
clang is detected a couple lines earlier for FreeBSD, which means if you are on FreeBSD and you have clang it is used, otherwise gcc.  I'm not sure this is really necessary here, but I didn't change it.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-11-18 16:00:30 UTC  
> Url: https://github.com/boostorg/build/pull/369#issuecomment-439703449  

clang is the system compiler on FreeBSD, that's probably why.

---
