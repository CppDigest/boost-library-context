# #253 - Cannot combine toolsets "gcc" and "gcc-7" in the same build with Boost.Spirit [Closed]

> Username: jeking3  
> Created at: 2017-10-27 19:59:06 UTC  
> Updated at: 2019-02-27 20:55:52 UTC  
> Closed at: 2019-02-27 00:03:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/253  

I added a top level Jamfile which builds example/ then test/.  Building example/ is a good idea to make sure examples don't rot.  I wasn't able to build with a fairly standard command:  
  
The command:  
```  
/boost/libs/spirit$ ../../b2 . toolset=gcc,gcc-7,clang cxxstd=03       variant=release,debug -j3  
```  
  
The result:  
```  
error: Name clash for '<pexample/qi/compiler_tutorial/bin/release>calc1'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <toolset-gcc:version>4.8  
error:     -  <toolset-gcc:version>7  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
I've used this successfully in Boost.Format and Boost.Uuid to build in Travis CI with gcc-4.8 and gcc-7.x in the same build job.  Not working for this project.  
  
https://travis-ci.org/jeking3/spirit/jobs/293831167

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-02-27 00:03:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/253#issuecomment-467666551  

This seems to be a problem with Boost.Build, I cannot even build Boost with multiple GCC/Clang toolsets https://github.com/boostorg/build/issues/399.

---

## Comment 2

> Username: jeking3  
> Created at: 2019-02-27 03:51:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/253#issuecomment-467713620  

multiple clang toolsets do not coexist (at least on linux), but one should be able to specify gcc-4.8, gcc-5, gcc-6, gcc-7 on the same line.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-27 14:13:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/253#issuecomment-467876416  

> multiple clang toolsets do not coexist (at least on linux)  
  
My Debian machine have multiple versions of Clang installed on the same time without any tweaking.  
  
I see a name clash here https://github.com/boostorg/spirit/blob/c36e74be1bd0b00b1b01f0bd5f33494f0274558c/example/qi/compiler_tutorial/Jamfile#L10-L11  
something should have changed in name clash detection and it now gives a slightly different error message, but also does not work when only GCCs or Clangs are used. I have posted this on linked Boost.Build issue.

---

## Comment 4

> Username: Kojoley  
> Created at: 2019-02-27 14:23:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/253#issuecomment-467880245  

Fixed in 5c1430406834967b1774e83d9ff11d0d1a44e861

---

## Comment 5

> Username: jeking3  
> Created at: 2019-02-27 20:55:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/253#issuecomment-468026851  

Nice - thanks!
