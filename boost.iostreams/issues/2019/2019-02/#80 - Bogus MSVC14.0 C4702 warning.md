# #80 - Bogus MSVC14.0 C4702 warning [Open]

> Username: Kojoley  
> Created at: 2019-02-28 23:00:28 UTC  
> Updated at: 2019-03-01 12:52:58 UTC  
> Url: https://github.com/boostorg/iostreams/issues/80  

I do not see them from MSVC14.1.  
```  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(257) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(258) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(259) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(261) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(262) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(257) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(258) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(259) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(261) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(262) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(433) : warning C4702: unreachable code  
boost\iostreams\detail\streambuf\indirect_streambuf.hpp(433) : warning C4702: unreachable code  
```

---

## Comment 1

> Username: mclow  
> Created at: 2019-03-01 01:13:01 UTC  
> Url: https://github.com/boostorg/iostreams/issues/80#issuecomment-468503268  

Can I have more information (boost version, etc)?  
Because here's the code I see:  
  
<img width="580" alt="screen shot 2019-02-28 at 5 12 39 pm" src="https://user-images.githubusercontent.com/167192/53609499-17a23280-3b7c-11e9-849c-a9014e739363.png">

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-03-01 01:18:32 UTC  
> Url: https://github.com/boostorg/iostreams/issues/80#issuecomment-468504330  

> boost version  
  
develop branch  
  
> etc  
  
I do not know what other information I can provide. It is exactly on those lines, it has to be a compiler bug because 14.1 does not produce those warnings, but I am not sure so posted as issue. If it ok I can open a PR with suppressing them otherwise I will just suppress the warning from the whole include on my side.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-03-01 12:43:26 UTC  
> Url: https://github.com/boostorg/iostreams/issues/80#issuecomment-468652869  

Look at the AppVeyor CI builds where MSVC 14.0 (2015) is tested:  
https://ci.appveyor.com/project/eldiener/iostreams/builds/22626745  
I only see:  
```  
c:\projects\boost-root\boost\test\impl\execution_monitor.ipp(1296) : warning C4702: unreachable code  
```  
  
Look at the regression test matrix for iostreams, for MSVC 14.0:  
https://www.boost.org/development/tests/develop/developer/iostreams.html  
I do not see it there (but I see what looks like 4 possibly bogus failure detections for MSVC 14.0.  
  
It does not show up in our test environments.  Please post a test and/or the git commit where you saw the issue so we can be looking at the correct code.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-03-01 12:52:58 UTC  
> Url: https://github.com/boostorg/iostreams/issues/80#issuecomment-468655234  

`b2 variant=release address-model=64 cxxflags=/w14702 warnings-as-errors=on toolset=msvc-14.0 libs/spirit/classic/test//tree_to_xml`
