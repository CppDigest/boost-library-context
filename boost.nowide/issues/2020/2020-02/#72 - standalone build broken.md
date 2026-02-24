# #72 - standalone build broken [Closed]

> Username: rwiesenfarth  
> Created at: 2020-02-27 15:01:14 UTC  
> Updated at: 2020-03-15 19:38:56 UTC  
> Closed at: 2020-03-15 19:38:56 UTC  
> Url: https://github.com/boostorg/nowide/issues/72  

Broken since c3dd00723f21a20fe5afb83f19c3eea8cbf9f4b1  
  
The removal of standalone/CMakeLists.txt breaks the script standalone/convert and thus the complete standalone version of nowide.  
  
Users interested in nowide are now forced to use Boost. Is this really intentional?

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-02-27 15:06:21 UTC  
> Url: https://github.com/boostorg/nowide/issues/72#issuecomment-592012016  

Yes. I'm still working on bringing this to a release ready state. Last thing remaining is C++11 support (move). Afterwards I'll update the stand-alone conversion script and add tests similar to what I did in my fork. The reason for removal of that CML is that I want a single-source version. So even the CMakeLists need to be generated out of the real CML which avoids different behavior of those versions.  
  
I'll also add tagged releases with ready-to-use tarballs of the standalone version
