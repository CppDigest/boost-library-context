# #1694 - Wrong requirement in Boost 1.70 API Changes Release notes [Closed]

> Username: philippeVerney  
> Created at: 2019-09-06 08:40:32 UTC  
> Updated at: 2019-10-18 23:20:05 UTC  
> Closed at: 2019-10-18 22:54:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1694  

### Version of Beast  
  
1.70.0  
  
### Steps necessary to reproduce the problem  
  
Compare requirement information on https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/introduction.html  
vs on release notes https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/release_notes.html  
  
### All relevant compiler information  
  
On one hand it is told : "Tested with these compilers: msvc-14+"  
On the other hand it is told : "On Windows, Visual Studio 2017 or later is required"  
  
From [Wikipedia](https://en.wikipedia.org/wiki/MSVC#Internal_version_numbering):  
Visual studio 2015 is known as msvc-14  
Visual studio 2017 is known as msvc-14.1

---

## Comment 1

> Username: philippeVerney  
> Created at: 2019-09-06 08:41:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-528767995  

I don't know where to change that. So I did not submit any PR.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-09 23:06:51 UTC  
> Updated at: 2019-09-09 23:07:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529700850  

> Visual studio 2015 is known as msvc-14  
  
It seems like Beast does build with Visual Studio 2015, at least according to Appveyor: https://github.com/boostorg/beast/blob/b7230f12f16fe7a9f7a1ece5be1f607c8552448a/appveyor.yml#L22  
  
This is using **msvc-14.0** which as you said is Visual Studio 2015? Something is definitely mislabeled because the image says 2017.

---

## Comment 3

> Username: philippeVerney  
> Created at: 2019-09-10 07:43:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529814579  

Hi Vinnie,  
  
msvc-14.0 comes indeed with VS2015. [Wikipedia ](https://en.wikipedia.org/wiki/MSVC#Internal_version_numbering)and this [MSFT blog](https://devblogs.microsoft.com/cppblog/side-by-side-minor-version-msvc-toolsets-in-visual-studio-2017/) confirm it.  
  
I am happy to see that Beast 1.70 (and 1.71) looks like working with VS2015 (msvc14).  
IMHO, it is strange to use a VS2017 image with a VS2015 toolset (msvc14) in your appveyor yml.  
But that's not a big problem.  
  
The main problem to me is that:  
- when I read the [documentation](https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/introduction.html), it looks that I can use VS2015 (msvc14).  
- when I read the [release notes](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/release_notes.html) (v1.70 "API changes" chapter) it looks that I **must** use at least VS2017  
  
I think now that the error is in the 1.70 "API changes" release notes : "On Windows, Visual Studio 2017 or later is required". I think that actually, this is not true. We can probably still use VS2015 with Beast 1.70. I'll give a look but I don't use all Beast functionalities at all.  
  
Maybe you know the reason of this 1.70 release notes line "On Windows, Visual Studio 2017 or later is required". It could clarify our discussion.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-09-10 12:27:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529911135  

> Maybe you know the reason of this 1.70 release notes line "On Windows, Visual Studio 2017 or later is required". It could clarify our discussion.  
  
Yep, I know exactly why - it is because I encountered a defect in VS2015 where it failed to compile correct code. We wrestled with it for a while but couldn't figure it out so we just dropped support for VS2015. After some time passed the code in question was either refactored or removed, and VS2015 started compiling again, but I forgot to change back the documentation.

---

## Comment 5

> Username: philippeVerney  
> Created at: 2019-09-10 12:31:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529912715  

Perfect!  
So I understand that VS2015 is the minimum required version and the 1.70 "API changes" release notes : "On Windows, Visual Studio 2017 or later is required" has to be ignored/deleted.  
  
Thanks a lot. It will help me to adopt 1.70 sooner.  
I let his issue opened if you want to keep it for cleaning release notes and/or appveyor script. But on my side, it can be closed.  
Thanks again

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-09-10 12:33:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529913409  

> So I understand that VS2015 is the minimum required version and the 1.70 "API changes" release notes : "On Windows, Visual Studio 2017 or later is required" has to be ignored/deleted.  
  
Beast in Boost 1.71 works under VS2015 again, but I'm not sure about 1.70, it might still contain the code that didn't compile correctly in VS2015. We would have to try it to know. Do you have VS2015 installed?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-09-10 12:34:22 UTC  
> Updated at: 2019-09-10 12:34:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529913673  

> IMHO, it is strange to use a VS2017 image with a VS2015 toolset (msvc14) in your appveyor yml.  
  
Yes I agree, and I investigated - the VS2017 image does in fact contain the VS2015 toolset. We did not invent those names, it is what Appveyor chose.

---

## Comment 8

> Username: philippeVerney  
> Created at: 2019-09-10 12:36:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529914351  

> Do you have VS2015 installed?  
  
Yes. But I never tried to build beast by myself (neither boost). I can give a look.

---

## Comment 9

> Username: philippeVerney  
> Created at: 2019-09-10 12:39:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-529915541  

> the VS2017 image does in fact contain the VS2015 toolset  
  
I thought you were not forced to give any toolset to appveyor but just the image. So giving only the VS2015 image would have been sufficient imho.  
For example , here is the appveyor script I use for my project (I don't give any information about the toolset but only about the image):   
https://github.com/F2I-Consulting/fesapi/blob/master/appveyor.yml

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-09-11 16:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-530461206  

No idea, but it seems to work and since I am barely proficient in Appveyor, I tend to leave it alone unless something breaks :)

---

## Comment 11

> Username: djarek  
> Created at: 2019-09-11 22:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-530587735  

IIRC I switched the image when I tried to do a build matrix on Appveyor and using the same image worked better for caching of the OpenSSL build. In the end, the matrix never got introduced (because of no parallel builds in Appveyor) but this enigmatic change remained :).

---

## Comment 12

> Username: stale[bot]  
> Created at: 2019-10-11 22:29:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-541244904  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: stale[bot]  
> Created at: 2019-10-18 22:54:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-543992686  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-10-18 23:20:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1694#issuecomment-544001433  

I updated the docs by the way
