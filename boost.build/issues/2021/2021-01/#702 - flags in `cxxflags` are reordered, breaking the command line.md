# #702 - flags in `cxxflags` are reordered, breaking the command line [Closed]

> Username: arvidn  
> Created at: 2021-01-11 09:48:17 UTC  
> Updated at: 2021-01-11 20:26:02 UTC  
> Closed at: 2021-01-11 20:26:02 UTC  
> Url: https://github.com/boostorg/build/issues/702  

using boost-build shipped with boost-1.73.0.  
  
```  
$ b2 --version  
B2 4.2-git  
```  
  
```  
$ b2 -v  
B2 Version 4.2. OS=LINUX.  
```  
  
When I specify `cxxflags` as a requirement on my target, the strings within that value appear to be split and reordered. Consider this example Jamfile:  
  
```  
import modules ;  
CXXFLAGS = [ modules.peek : CXXFLAGS ] ;  
lib a : a.cpp : <cxxflags>"$(CXXFLAGS)" ;  
```  
  
If I invoke that with this command (using the default configured GCC toolset):  
  
```  
$ CXXFLAGS="-pipe -isystem /usr/local/include" b2 -an  
```  
  
This build command line is printed:  
  
```  
"g++"   -fPIC -O0 -fno-inline -Wall -g -isystem -pipe /usr/local/include    -c -o "bin/gcc-9.2.0/debug/a.o" "a.cpp"  
```  
  
Note how `-pipe` and `-isystem` were flipped, causing `-pipe` to be treated as a search path!  
  
If I instead invoke this build passing in the flags via the `cxxflags` feature on the command line (not going via the environment variable):  
  
```  
$ b2 cxxflags="-pipe -isystem /usr/local/include" -an  
```  
  
I get the correct behavior:  
  
```  
"g++"   -fPIC -O0 -fno-inline -Wall -g -pipe -isystem /usr/local/include    -c -o "bin/gcc-9.2.0/debug/a.o" "a.cpp"  
```  
  
I have not been able to understand why this feature is treated differently or what possibly could cause it to reorder the strings. This also affects the `clang-linux` toolset.  
  
It's causing a compile failure on FreeBSD [here](https://github.com/arvidn/libtorrent/issues/5797).

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-11 13:16:18 UTC  
> Updated at: 2021-01-11 13:16:42 UTC  
> Url: https://github.com/boostorg/build/issues/702#issuecomment-757944495  

This is caused by the Jam language variable expansion semantics.  
  
In the first case of the `<cxxflags>"$(CXXFLAGS)"` expression, with you value, it is expanded to `<cxxflags>"-pipe" <cxxflags>"-isystem" <cxxflags>"/use/local/include"`. And b2 then reorders those *individual* properties as it sees fit.  
  
For the second case of `cxxflags="-pipe -isystem /usr/local/include"` it expands that to `<cxxflags>"-pipe -isystem /usr/local/include"` only. I.e. into a single property. Which is then pasted into the command as a whole.  
  
To get the equivalent of the latter in the former you need to convert the `$(CXXFLAGS)` into a single space separated value like this:  
  
```  
import modules ;  
CXXFLAGS = [ modules.peek : CXXFLAGS ] ;  
lib a : a.cpp : <cxxflags>"$(CXXFLAGS:J= )" ;  
```  
  
The `:J= ` joins the individual list elements with a space. Note that this has the drawback of not dealing with spaces in paths or quoting that may be needed. It's best to provide special options/flags instead of direct use of something like `CXXFLAGS` environment variables.
