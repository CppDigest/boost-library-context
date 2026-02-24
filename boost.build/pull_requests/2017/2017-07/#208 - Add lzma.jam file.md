# #208 Add lzma.jam file. [Merged]

> Username: rdoeffinger  
> Created at: 2017-07-03 17:54:32 UTC  
> Updated at: 2021-10-02 22:08:21 UTC  
> Merged at: 2017-07-17 22:23:47 UTC  
> Closed at: 2017-07-17 22:23:47 UTC  
> Url: https://github.com/boostorg/build/pull/208  

Just a quick hack to allow to implement LZMA  
compressor and decompressor in iostreams (see also  
trac ticket 7534).  
Probably needs to be refined at some point by someone  
who knows what they are doing, but it seems to work in principle.

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2017-07-12 12:11:48 UTC  
> Url: https://github.com/boostorg/build/pull/208#issuecomment-314750281  

Any comments? I know it's not ideal to just add a (currently) unused file, but it seems the simplest approach.  
The iostreams change depending on this: https://github.com/boostorg/iostreams/pull/38

---
