# #38 Add lzma compressor and decompressor filters. [Merged]

> Username: rdoeffinger  
> Created at: 2017-07-03 17:55:11 UTC  
> Updated at: 2017-07-26 08:47:23 UTC  
> Merged at: 2017-07-26 00:54:41 UTC  
> Closed at: 2017-07-26 00:54:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38  

Needs lzma.jam file in tools/build module.  
See also trac ticket 7534.  
The code has been around in random places since some time,  
I only added tests and build system updates and  
minor fixes for issues those tests discovered.

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2017-07-12 12:12:25 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-314750406  

Note: this fails because it needs the lzma build file from https://github.com/boostorg/build/pull/208

---

## Comment 2

> Username: eldiener  
> Created_at: 2017-07-17 22:49:06 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-315907396  

I have two issues:  
  
1) Now that your lzma.jam file has been added to Boost Build don't you need some documentation in Boost Build how to use lzma ? If I am going to test your lzma code to make sure it works I need to know how to setup an lzma toolset in Boost Build.  
  
2) Are you willing to maintain the lzma implementation in iostreams in the near future in case of problems or suggestions ?

---

## Comment 3

> Username: rdoeffinger  
> Created_at: 2017-07-18 11:39:12 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-316038108  

To split things up, on 1.:  
- To test/use the lzma code, you should just need to have the lzma headers and library (and maybe pkg-config? I am rather unclear what the build system uses/how it detects things). I don't know what the CI system here provides, hopefully the logs of this run will show if it picks it up or not.  
- There is no support for integrated build from source like it looks there is for e.g. zlib. Mostly because I had no idea how to use it and it would have been quite some effort, but also because it seems like really bad practice so I don't really want to promote that.  
- I did extend the documentation a bit (I didn't find it when I looked for it the first time). But I only really tested compiling against default system libraries, and only using dynamic linking. The build system is rather a mystery to me so I feel happy I got it to work at all.  
  
On 2.:  
Short/medium term I will have interest in having this work well, so yes I can justify spending additional time on looking into reported issues. Suggestions would depend on value to me/how convincing and how much effort the suggestion is.  
That is least if someone pings me on it either via email or github, monitoring the main bug tracker or the overall boost mailing list seems a bit high cost/benefit, so I would prefer to avoid that (even though I in principle find mailing lists better for collaboration than github).

---

## Comment 4

> Username: rdoeffinger  
> Created_at: 2017-07-18 11:41:06 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-316038497  

And the results tell me Travis is setup without LZMA and I obviously never tested without LZMA, and that is actually broken... That one I'll obviously fix.

---

## Comment 5

> Username: rdoeffinger  
> Created_at: 2017-07-18 13:09:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-316058469  

Ah, weird. The tests work such that it expects tests for disabled features to still compile but fail at linking?  
Had to remove the lzma.h include from the .hpp file to fix that (admittedly it seems a good idea anyway, protects the lzma.h header and boost user's code to interfere with each other).  
Hope to push an update soon.

---

## Comment 6

> Username: rdoeffinger  
> Created_at: 2017-07-18 14:43:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-316086351  

Ok, I don't know how to make the test happy. As far as I can tell behaviour is identical to bzip2.  
Is the bzip2 test broken on systems with no bzip2 installed? Or am I missing some trick?

---

## Comment 7

> Username: rdoeffinger  
> Created_at: 2017-07-24 15:22:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317458363  

Ok, I figured out a way to make the test conditional, hopefully that now passes the CI tests (though it won't actually test the feature unless liblzma-dev dependency is requested on the server, no idea who maintains that).  
However the bzip2 and gzip tests are also buggy and will fail if these libraries are not available (I just never realized they actually failed, it said the test was skipped because it didn't compile, only checking the exit code made it clear it's considered a failure).  
I also suspect that the test Jamfile fails to consider the NO_COMPRESSION option, too.

---

## Comment 8

> Username: eldiener  
> Created_at: 2017-07-25 07:37:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317655852  

I could not get your changes to work on Windows so I will describe what I did.   
  
I downloaded the lzma SDK from 7-zip at http://www.7-zip.org/sdk.html. The only build for the lzma dll was for ANSI-C and I built the library using Visual Studio 2017. I changed the build so that it produced an lzma.dll and lzma.lib import library combination since the original names were lzmalib.dll/lzmalib.lib. I created a symbolic link for the lzmalib.h header as lzma.h.  
  
I then added the line:  
  
using lzma : 16.0.4 : <search>E:/Utilities/lzma/v1604/C/Util/LzmaLib/Release <include>E:/Utilities/lzma/v1604/C ;  
  
to my user-config.jam file.  
  
I then tried running the the tests with your changes but it gave a bunch of errors trying to compile src/lzma.cpp starting with:  
  
..\..\..\libs\iostreams\src\lzma.cpp:30:34: error: 'LZMA_OK' was not declared in this scope  
 const int okay                 = LZMA_OK;  
                                  ^~~~~~~  
..\..\..\libs\iostreams\src\lzma.cpp:30:34: note: suggested alternative: 'SZ_OK'  
 const int okay                 = LZMA_OK;  
                                  ^~~~~~~  
                                  SZ_OK  
..\..\..\libs\iostreams\src\lzma.cpp:31:34: error: 'LZMA_STREAM_END' was not declared in this scope  
 const int stream_end           = LZMA_STREAM_END;  
                                  ^~~~~~~~~~~~~~~  
..\..\..\libs\iostreams\src\lzma.cpp:31:34: note: suggested alternative: 'SZ_SEEK_END'  
 const int stream_end           = LZMA_STREAM_END;  
                                  ^~~~~~~~~~~~~~~  
                                  SZ_SEEK_END  
  
etc.  
  
So evidently the lzmalib.h, which I symlinked as lzma.h does not contain those LZMA definitions and when I searched for LZMA_OK in the lzma SDK files it does not exist anywhere.

---

## Comment 9

> Username: rdoeffinger  
> Created_at: 2017-07-25 08:46:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317671764  

Sorry for wasting your time like that... I only ever used a Google search for liblzma, which points you to the right thing. The LZMA SDK is the wrong thing...  
The library you need is at https://tukaani.org/xz/, more specifically under https://git.tukaani.org/?p=xz.git;a=tree (there is a point that naming it libxz might have been a more fortunate choice).  
It doesn't look like it supports VisualStudio, though I admit that I simply grabbed the binary and header file from MinGW (if I remember right) when I had to use it on Windows.

---

## Comment 10

> Username: eldiener  
> Created_at: 2017-07-25 13:10:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317732188  

I will work with this library so I can test your code. But I think you need to add to the docs that the library your lzma implementation supports is this one and not the 7zip lzma sdk, else other users will be as confused as I was.

---

## Comment 11

> Username: rdoeffinger  
> Created_at: 2017-07-25 14:25:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317754458  

Yes, I fully agree it should be written down, I just didn't even think of it as a problem.  
I updated the change with documentation clarifying this and adding a link to the right project.  
I am a bit unsure if it will end up being read that much, but at least it is there.

---

## Comment 12

> Username: eldiener  
> Created_at: 2017-07-25 15:07:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317768195  

I have your code working correctly on Windows with the xz utilities implementation of liblzma. I just want to make sure the authors of the lzma source and header files are correct. Have these people been notified of the code which will be added to iostreams. I know that Jonathan Turkanis is the original library developer for iostreams but I am loth to add this code without his consent. I do not know who Milan Svoboda is.

---

## Comment 13

> Username: rdoeffinger  
> Created_at: 2017-07-25 15:24:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317774200  

@tex I think we are talking about you, and you are the original author of the boost lzma filter.  
Do you have any objections or concerns about integrating it?  
I assumed (also due to the license) you just never had the time/motivation for it.

---

## Comment 14

> Username: rdoeffinger  
> Created_at: 2017-07-25 15:41:34 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317779531  

On looking a bit, I suspect Jonathan Turkanis is mentioned due to the file originally being a copy-paste from bzip2.cpp.

---

## Comment 15

> Username: rdoeffinger  
> Created_at: 2017-07-25 16:05:14 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317786696  

Not sure what the policy on this is, but the files seem based on bzip2.cpp/hpp and modified.  
I re-implemented the relevant fixes to the bzip2 I could find from the log in a separate patch, so at least they are not based on outdated code.

---

## Comment 16

> Username: eldiener  
> Created_at: 2017-07-25 17:53:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317817285  

For  include/boost/iostreams/filter/lzma.hpp the copyright notice says "// (C) Copyright Milan Svoboda 2008." You have added that file. Who is Milan Svoboda ? For  src/lzma.cpp the copyright notice says "// (C) Copyright Jonathan Turkanis 2003." You have added that file. For  test/lzma_test.cpp the copyright notice says "// (C) COPYRIGHT 2017 ARM Limited // (C) Copyright 2008 CodeRage, LLC (turkanis at coderage dot com) // (C) Copyright 2004-2007 Jonathan Turkanis".  You have added that file. If you indeed have created these files the copyright notice should only be in your name. If others have created these files then you need to get permission from Milan Svoboda and Jonathan Turkanis to use their work. I am neither of those people. Do you understand what I am saying to you ?

---

## Comment 17

> Username: tex  
> Created_at: 2017-07-25 19:21:08 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317844336  

Hello, yes I'm Milan Svoboda. I have no objections against integrating it.

---

## Comment 18

> Username: tex  
> Created_at: 2017-07-25 19:27:47 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317846140  

Frankly, it's been a long time ago, I do not remember this too much. Is this something form my FuseCompress project?

---

## Comment 19

> Username: swatanabe  
> Created_at: 2017-07-25 19:31:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317847200  

AMDG  
  
On 07/25/2017 11:57 AM, Edward Diener wrote:  
>  If others have created these files then you need to get permission from Milan Svoboda and Jonathan Turkanis to use their work. I am neither of those people. Do you understand what I am saying to you ?  
>   
  
  Jonathan Turkanis is the original author of Boost.IOStreams and  
the copyright notice almost certainly comes from copy/pasting  
existing IOStreams code (which is already Boost licensed).  
  
In Christ,  
Steven Watanabe

---

## Comment 20

> Username: rdoeffinger  
> Created_at: 2017-07-25 19:41:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317849760  

I do well know that people need to have given proper licenses, the unclear points are however:  
- If these people published the files under a suitable (in this case, boost) license, as made clear by the copyright header, do you require them to explicitly state once more that they are ok with including it in boost? http://www.boost.org/development/requirements.html#License and similar FAQ items didn't read to me like it. (this point is somewhat moot as Milan already responded)  
-  If, like for test/lzma_test.cpp as the comment says I use an existing boost file (gzip_test.cpp) as basic template, do you then want me to ONLY have my Copyright (in this case, that means the ARM Copyright)? I assumed that as it is a derived work (though you can discuss if the parts copied are copyrightable) I should preserve the copyright headers from the gzip_test.cpp. I would also assume I would not have to ask permission to re-use code already in boost for new contributions.  
  
From my point of view, these are pure contribution policy questions, but I don't think I did anything obviously wrong here, so please tell me if there's still something you think is wrong here.

---

## Comment 21

> Username: rdoeffinger  
> Created_at: 2017-07-25 19:44:13 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317850500  

@tex yes, it is from your fusecompress source code.  
  
Just to be absolutely clear: I did of course not add these license headers myself, they are from the files as originally written by their respective authors, only in case of the test file did I add the ARM one in addition as a relevant amount of code was added.

---

## Comment 22

> Username: eldiener  
> Created_at: 2017-07-25 22:06:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317887068  

If you are copy/pasting other code but changing it for the purposes of lzma I think the copyright should be your own, but if you think enough of it is substantially someone else's I would say that you should add your own copyright to the original. If you are just taking someone else's code and incorporating it whole then I think the copyright should remain only theirs. I do not know how much of the lzma code copy/pasted from Jonathan Turkanis code was changed by you for lzma, but I think at minimum you should add your own copyright to what was there before. If you feel you have already followed these guidelines I have no problem merging the pull request. What I am bothered by is that someone will ses Jonathan Turkanis copyright on lzma code and think he actually authored it when in fact you did.

---

## Comment 23

> Username: rdoeffinger  
> Created_at: 2017-07-26 08:47:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/38#issuecomment-317990256  

I hope you didn't feel hurried to merge it, I mostly got confused about what you needed clarification on and a bit defensive since I don't want to look like I wasn't careful about the licensing.  
Your point about the authorship being not well declared is however completely right, normally that isn't an issue because one would check git history, but that doesn't work here.  
Never had exactly that situation so not sure how to handle it best, but I'll push a patch (hopefully not messing up this pull request that way) and you can check if you think it is an improvement that should be added.

---
