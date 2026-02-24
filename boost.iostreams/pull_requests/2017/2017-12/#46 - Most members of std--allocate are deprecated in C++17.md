# #46 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-28 15:31:03 UTC  
> Updated at: 2018-01-14 07:52:37 UTC  
> Merged at: 2017-12-29 04:26:25 UTC  
> Closed at: 2017-12-29 04:26:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/46  

Replace them by their cousins from std::allocator_traits.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-12-28 16:27:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/46#issuecomment-354315237  

It looks like Appveyor isn't configured correctly ...

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-12-28 17:17:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/46#issuecomment-354322993  

I think the problem with Appveyor is in those 3rd party libraries which iostream supports, like zlib, bzip2, and lzma. While I have manually set them up for myself when testing iostreams under Windows I haven't a clue how top get Appveyor to set them up for testing in its environment. If you or anybody else knows how to do it, feel free to create a PR for the Appveyo yml file. In general I personally do not like to waste time figuring out Appveyor ( or Travis CI ) when I can run the tests successfully on my local machine.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-12-28 17:24:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/46#issuecomment-354323887  

Well, on my local machine the tests build and pass just fine. Regarding Appveyor: I'm totally blank on this CI thing and their configuration files.

---
