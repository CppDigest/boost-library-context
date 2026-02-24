# #48 Add zstd compression/decompression filter. [Merged]

> Username: rdoeffinger  
> Created at: 2018-01-14 13:08:50 UTC  
> Updated at: 2018-03-27 19:40:17 UTC  
> Merged at: 2018-03-26 21:15:37 UTC  
> Closed at: 2018-03-26 21:15:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48  

Implements trac issue #13356.  
Not tested in real production use, help welcome.  
  
Depends on "build" PR that adds zstd.jam (though it is just a search-and-replace of lzma.jam)

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2018-01-19 21:26:19 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-359093534  

Hmm... AppVeyor build is still failing, but I think the remaining errors have nothing to do with me but rather the zlib and bzip2 tests being enabled even when those libraries are not available.

---

## Comment 2

> Username: rdoeffinger  
> Created_at: 2018-03-24 19:22:09 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-375918141  

Sorry, I had forgotten this was still open.  
Haven't seen any issues in the (limited) use since.  
Could you please consider merging?

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-03-24 20:02:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-375920901  

What are the actual lib file names zstd looks for on Windiow and on Linux ? I need to test this to see if it is working and I usually work on Windows so I need to test this both on Windows and Linux.

---

## Comment 4

> Username: rdoeffinger  
> Created_at: 2018-03-24 20:25:02 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-375922346  

If that helps: the logic is the same as for other libraries like liblzma, libbz2 etc.  
Otherwise I can say it works with the standard libzstd(-dev) packages on Linux, so should be libzstd.so  
On Windows I would then expect it to want libzstd.dll  
I'd hope the binaries from here would work, the dll seems to match that naming at least: https://github.com/facebook/zstd/releases

---

## Comment 5

> Username: eldiener  
> Created_at: 2018-03-24 20:49:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-375923896  

It needs to find an import library under Windows. Is that libzstd.lib or libzstd.a ? In the zstd I downloaded for Windows the import library is libzstd.lib. For bzip2 it is bzip2.lib but it is found when I specify <name>bzip2 in my toolset configuration. For zlib it is zdll.lib but it is found under Windows for some reason although I would have expected it to be looking for libzlib.lib ( or libzlib.a ). For lzma it is actually liblzma.a in my installation on Windows, but it seems to be found nonetheless. Maybe the .jam code checks for both the .lib and the .a extension on Windows. I assume on Linux it is always lib(name of library).a by default. I think there is enough confusion of what these import library names are supposed to be that they really need to be specifically documented for the end-users so they know what import library to expect and also know what header file name is going to be included.  
  
I am also concerned that these 3rd party compression toolsets which are added to iostream be documented under the Boost Build documentation which specifies 3rd party toolsets to use. It does no good adding compression toolsets to iostream if end-users do not know what they need on the various OSs to use them.  
  
I do appreciate your integrating zstd into iostream. I just do not want to have to deal with end-user who can not use zstd properly due to confusion about what zstd is specifically looking for, especially outside of Linux.

---

## Comment 6

> Username: rdoeffinger  
> Created_at: 2018-03-24 21:22:50 UTC  
> Updated_at: 2018-03-24 21:26:29 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-375925878  

Unfortunately I don't use Windows much and I know even less how the boost build system works.  
  
I can tell you some theories on how it should work:  
The libzstd.dll should be detected and work on its own at least with e.g. MinGW gcc.  
If MSVC is used it might insist on the .def or .lib file, but I never really understood that whole mess or why it still exists nowadays (numeric imports being the only good reason I know, and not using those has been best practice since decades).  
If liblzma.a works for you that would suggest that it is configured for static linking, or at least chooses that. And it also means libzstd.a should also work. However the build system OUGHT to look for libzstd.lib instead for static linking on Windows (and in the zip download it would need the static/libzstd_static.lib to be renamed thus).  
  
Lastly, from what I can tell from the build system, it should be possible to explicitly specify the name of the library, same as specifying the include path, just using &lt;name&gt;.  
  
The reason zdll.lib works is this line in zlib.jam:  
names = z zlib zll zdll ;  
So at least zlib.lib should work as well (I don't know if libzlib.lib would work, the lib prefix might only be used on Linux, this should be some magic triggered by set-default-names from ac-library, more specifically it seems like find-library adds it, but I can't really read the .jam code).  
Looking at that code, it seems even magic environment variables can be used to set all this up.  
  
I hope this helps, and I understand and appreciate your desire to understand how this works and make it usable  
But I kind of think that is a matter of general documentation of the build system, because this is all handled by generic code that applies to any and all of the about dozens of libraries boost links against.  
Users shouldn't have to trawl through a separate and likely half-outdated and half-wrong version for each of them when it could be documented properly once... (no intention to lay blame here, just opinion on what I consider the proper solution to that)

---

## Comment 7

> Username: eldiener  
> Created_at: 2018-03-26 17:43:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376251472  

The DLL is irrelevant. It is the import lib for the DLL on Windows and the header file name that counts. It is up to the end-user to make sure the DLL is in the Windows path, not Boost. The toolsets designate where to find the import library and the header file.   
  
It is important for the end-user to know what the import library name(s) and header file name is expected for each of the iostream compression libraries. Remember that to use these compression libraries, whether on Windows or Linux, the end-user must create the correct toolset definition somewhere, usually user_confiig.jam. Without knowing what these compression libraries expect as the import library name or the header file name, how can the end-user be successful in setting it up for himself ? This is the point I am trying to make. When the end-user does not know how to setup use for zstd on his OS, what do we tell them ? This is why I am concerned that we are not documenting what the end-user should expect when setting up these compression library toolsets for different OSs and compilers.  
  
I will try to test your PR out on Windows and see if it works for me using the Windows zstd implementation I downloaded and installed and both the vc++ and mingw-64/gcc compilers.

---

## Comment 8

> Username: rdoeffinger  
> Created_at: 2018-03-26 19:11:36 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376278787  

Thanks a lot for your efforts. Likely I am missing something, but the import lib seems a useless artefact that doesn't serve any valid use-case anymore.  
On Linux there is no need to create a user_config.jam, it actually all just works, and honestly if creating one is necessary on Windows I'd say that's where the real problem lies in all this.  
  
But to not just be complaining, I tried cross-compiling for Windows and the only thing I had to do in the configs was to set up the gcc name via "using gcc : mingw : x86_64-w64-mingw32-g++ ;" - it would have been even nicer if the build system supported a cross-compilation prefix like most others.  
  
I unpacked the downloaded zstd zip into zstd64 directory and ran:  
./b2 target-os=windows toolset=gcc-mingw library-path=$pwd/zstd64/dll include=$pwd/zstd64/include link=shared variant=debug address-model=64  
Static will probably be a bit more complicated due to the "wrong" name of the .lib, but not too bad I hope. It would also be nice if I didn't have to specify stuff like address-model when the specified compiler doesn't even support 64-bit but whatever.  
For some reason I then had to hack out a -mthread option (not recognized by Mingw-gcc), but otherwise it worked.  
And it worked even when I deleted the .def and .lib files, proving them useless.  
The tests didn't really work because boost/test/utils/setcolor.hpp included Windows.h instead of windows.h  
Well, and I didn't figure out how to actually make the tests run, if there's a way to automatically wrap the commands into a "wine" call I didn't find it.  
Overall, making it find the lib was the smallest issue of all...

---

## Comment 9

> Username: eldiener  
> Created_at: 2018-03-26 21:15:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376314215  

You are speaking about cross-compiling on Linux, not compiling on Windows. These are two different worlds. I can understand that you work on Linux and do not care if zstd works on Windows, but there are many Windows programmers who use vc++ who do care.  
  
My results are that when building the iostreams library on Windows with mingw-64/gcc-7.2 the library finds zlib, bzip2, lzma, and zstd based on my user-config.jam entries. But when building the iostreams library on Windows with msvc-14.1 the library finds only zlib and bzip2 and does not find lzma and zstd. To me this is not good. I have user-config.jam entries for all four compression libraries, so it would be good to know why lzma and zstd can not be used based on these entries for iostreams. Nonetheless I will push through this PR and do further testing on it on the 'develop' branch to try to understand why it does not find the zstd implementation on Windows.

---

## Comment 10

> Username: rdoeffinger  
> Created_at: 2018-03-27 00:27:18 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376355367  

First, TL;DR version:  
Could you try this patch?  
https://github.com/rdoeffinger/build/commit/483c1135abf5778c56e3fe6b3f57d59c17dc5d3f  
I suspect that the build system doesn't actually prepend lib* itself, it relies on gcc (thus mingw works) and MSVC builds then don't work.  
  
Long version:  
Sorry, I think I am being a pain. But I hope you'll forgive me for pushing my point a bit more.  
It's not really that I don't care about Windows (though I find it too much pain to set up and try myself right now).  
I am complaining because I think renaming libraries to magic names and fiddling with user-config.jam is not even remotely an acceptable solution. This user-config.jam I'm sure is a nice feature for advanced users, but everyone else shouldn't have a reason to have to deal with it.  
Since they aren't system libraries on Windows, avoiding the library-path= and include= command-line options might not be possible, but anything more than that IMHO should be considered a usability bug in the build system.  
I think the goal should be that the description for building with these is essentially: download all those binaries provided by the projects, extract and dump all the .dll and .lib files in one directory, all .h in another, and then just add library-path= and include= to point to these directories, done.

---

## Comment 11

> Username: eldiener  
> Created_at: 2018-03-27 15:39:37 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376572108  

Your patch worked for vc++ and iostreams was able to build with lzma and zstd. Bravo ! If you have not submitted your patch yet to Boost Build as a PR, please do so.  
  
I understand your point about 'library-path' and 'include' but for many users it is easier to setup the toolset for a 3rd party library rather than have to remember to add 'library-path' and 'include'  on the b2 command line. Furthermore the issue which your patch fixes has nothing to do with whether someone uses 'library-path' and 'include' or whether someone uses toolsets for 3rd party libraries, but rather has to do with the library variant names on different OSs for a library.

---

## Comment 12

> Username: rdoeffinger  
> Created_at: 2018-03-27 19:40:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/48#issuecomment-376649396  

Submitted it now, thanks for testing.  
I guess the command-line thing was more for the case of a first-time user who just wants it to get working at all to start with.  
I meant this in the context of "how should people figure out how to use it" and how it might be simplified.  
I admit I was actually thinking if it wouldn't be nice if some thirdparty/lib and thirdparty/include was added to those paths by default so that users would just need to drop them there on Windows.  
But as I rarely use Windows for development it's probably best to leave it to the people actually affected.  
So thanks, and maybe until some other PR. Now that I know how it works I might even give Windows users a little consideration from the start next time ;)

---
