# #127 - Error when compiling client code "could not find boost_python-vc140-mt-1_64.lib" [Closed]

> Username: youcefl  
> Created at: 2017-05-10 13:58:44 UTC  
> Updated at: 2017-06-12 13:43:12 UTC  
> Closed at: 2017-06-12 13:32:33 UTC  
> Url: https://github.com/boostorg/python/issues/127  

When compiling Boost libraries I used to get two lib/dll files for Boost.Python e.g.:  
boost_python-vc120-mt-1_60.lib and boost_python3-vc120-mt-1_60.lib  
(even though I only targeted Python 3.4), now, building version 1.64, I only get  
boost_python3-vc140-mt-1_64.lib so the automatic linking does not work anymore  
because the file <boost/python/detail/config.hpp> only has  
#define BOOST_LIB_NAME boost_python  
Is it possible to restore the old behavior ? I could create a symbolic link  
boost_python-vc140-mt-1_64.lib -> boost_python3-vc140-mt-1_64.lib  
but I'd prefer not having to do so.

---

## Comment 1

> Username: jakirkham  
> Created at: 2017-06-10 22:08:40 UTC  
> Updated at: 2017-06-10 22:24:25 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307593044  

Also ran into this issue as well.  
  
Was discussing this with a colleague and we agree that the issue is likely this [line]( https://github.com/boostorg/python/blob/boost-1.64.0/include/boost/python/detail/config.hpp#L108 ). It should probably be replaced with code like this (borrowed from this [comment]( https://github.com/ukoethe/vigra/issues/410#issuecomment-307552169 )).  
  
```c++  
#if PY_MAJOR_VERSION < 3  
# define BOOST_LIB_NAME boost_python  
#else  
# define BOOST_LIB_NAME boost_python3  
#endif  
```  
  
Thoughts on this @stefanseefeld?  
  
Edit: Added PR ( https://github.com/boostorg/python/pull/135 ) along these lines.

---

## Comment 2

> Username: jakirkham  
> Created at: 2017-06-10 22:16:29 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307593355  

Might also be worth checking a few [other places]( https://github.com/boostorg/python/search?utf8=%E2%9C%93&q=boost_python&type= ) just to be sure.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-06-11 01:30:11 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307600086  

Thanks for investigating this. I'm neither a Windows developer nor a Windows user, so this issue is somewhat outside my own even horizon.  
Yes, the problem is the above line as far as I understand. However, it isn't entirely clear to me what the "right" fix is. Do we really want to hardcode the name of the library based on what Python version is used ? I think it might be better to keep the hardcoded definition but conditionalize it:  
```  
#ifndef BOOST_LIB_NAME  
# define BOOST_LIB_NAME boost_python  
#endif  
```  
and then let the build system provide its macro to override this default.

---

## Comment 4

> Username: jakirkham  
> Created at: 2017-06-11 17:13:37 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307642964  

Sorry I'm unclear on this. What build system is defining `BOOST_LIB_NAME`? Also why shouldn't this be something Boost.Python provides?

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-06-11 17:50:46 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307645284  

The build system (the Jamfile right now, that is) encodes how the compiled library will be named, so I think that's the logical place to define the BOOST_LIB_NAME macro (i.e., add `-DBOOST_LIB_NAME=boost_python`). If we define the macro in one of the source files, we have to modify two places if ever we want to change the name of the library, rather than one.  
Thus my proposal is to conditionalize the definition in the config.hpp header (as I outlined in the previous  comment), and add the appropriate logic to the Jamfile (SConscript, etc.) that defines the build process.

---

## Comment 6

> Username: jakirkham  
> Created at: 2017-06-11 19:52:30 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307652455  

So the problem that we are encountering is not with building Boost.Python. Instead it is another library that relies on Boost.Python, which is built using CMake. Given this is a common way to build cross platform libraries (particular to allow builds on Windows), I expect this problem is quite common. Perhaps this is the missing context for this discussion. 😉 Sorry for not making that clear.  
  
Despite our [best efforts]( https://github.com/ukoethe/vigra/blob/Version-1-11-0/CMakeLists.txt#L177-L194 ) to pick up the correct name for Boost.Python (and [success in doing so]( https://ci.appveyor.com/project/jakirkham/vigra-feedstock/build/1.0.3/job/afj486ikt3dyd6tp#L465 )), it seems that `BOOST_LIB_NAME` is [throwing us for a loop]( https://ci.appveyor.com/project/jakirkham/vigra-feedstock/build/1.0.3/job/afj486ikt3dyd6tp#L665 ). ( https://github.com/ukoethe/vigra/issues/410 ) I'm not sure that fixing this in the Jamfile, SConscript, etc. will help us. Though I must admit I'm not too sure of what all is getting pulled into this build step from the Boost side.  
  
Now I suppose we could define `BOOST_LIB_NAME` somewhere in our code (though I'm not sure where), which would allow us to solve the problem. However it does mean the build logic for linking to Boost.Python is being shoved off on downstream libraries making the whole process more complex (especially for new users of Boost.Python). This is also keeping in mind the [amount of logic]( https://github.com/ukoethe/vigra/blob/Version-1-11-0/CMakeLists.txt#L126-L194 ) already spent trying to correctly identify Boost libraries. Not to mention this variable has the feeling of something internal to Boost and is pretty generically named. I don't feel particularly comfortable building a library that uses multiple things from Boost and specifying something like `BOOST_LIB_NAME`. It may have unforeseen consequences both now and in the future. Especially considering it is already defined differently in Boost.Python thanks to Boost.NumPy using it [as well]( https://github.com/boostorg/python/blob/boost-1.64.0/include/boost/python/numpy/config.hpp#L65 ).  
  
FWIW our strategy, proposed in the PR, works for our build. I'm not entirely clear on how changing Boost.Python's build files will help us though. 😕 That said, I'm not picky. If you have a patch in mind, I could give it a try and get back to you. 😄

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-06-12 13:36:50 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307791393  

OK, after sleeping over this and considering the different options, I think your patch is the best option. I'm still a bit uneasy about the whole auto-linking machinery, but then I'm not a Windows user so there are lots of things that seem outlandish to me. :-)  
Thanks for the patch !  
(And please re-open the issue should the original problem resurface.)

---

## Comment 8

> Username: jakirkham  
> Created at: 2017-06-12 13:43:12 UTC  
> Url: https://github.com/boostorg/python/issues/127#issuecomment-307793184  

Thanks @stefanseefeld.
