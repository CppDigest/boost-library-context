# #614 Add a Boost-friendly subproject case to CMakeLists [Merged]

> Username: pdimov  
> Created at: 2021-06-04 17:54:12 UTC  
> Updated at: 2021-12-15 15:08:21 UTC  
> Merged at: 2021-10-30 21:14:16 UTC  
> Closed at: 2021-10-30 21:14:16 UTC  
> Url: https://github.com/boostorg/gil/pull/614  



---

## Comment 1

> Username: sdebionne  
> Created_at: 2021-06-15 19:57:59 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-861790891  

Shall we unify the subproject and standalone configurations (e.g project name `boost_gil` vs `Boost.GIL`) and add options / dependencies for the io extensions in the subproject configuration? Or it's OK if they are decorrelated?

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-06-15 22:13:12 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-861870674  

You can unify them if you like, but it's not required (or urgent.)

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-09-16 11:39:42 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-920826948  

Can this please be merged (incl. to master), so that I can remove `gil` from `BOOST_INCOMPATIBLE_LIBRARIES`?

---

## Comment 4

> Username: lpranam  
> Created_at: 2021-09-16 15:26:16 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-921002925  

obviously we can get it in . if not much to ask can we keep license on top of the file or is there any reason to keep the new configuration at top?

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-09-16 15:32:18 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-921007959  

Feel free to reorder the comments in any way you like. :-)

---

## Comment 6

> Username: mloskot  
> Created_at: 2021-09-17 08:18:46 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-921603685  

Clearly, I've failed to take care of this. Thanks @lpranam & @pdimov   
  
@lpranam Please, merge when you think it's ready.

---

## Comment 7

> Username: pdimov  
> Created_at: 2021-09-17 11:12:14 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-921712758  

The comment at the top  
```  
# **WARNING:**  
# The CMake configuration is only provided for convenience  
# of contributors. It does not export or install any targets,  
# deploy config files or support subproject workflow.  
```  
is no longer strictly accurate, so you might either rephrase it or move it after the `else()`.

---

## Comment 8

> Username: simmplecoder  
> Created_at: 2021-09-18 17:14:58 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-922341765  

I believe in the current state the library will not be usable due to missing IO dependency libraries and some builds indicate that internal boost dependencies might be missing too (align library is not in the target link libraries list but is used as include).  
  
@pdimov could you please point to a place where the expected behavior of compatible libraries is documented? Should they just provide the new target names? In that case I believe it would be better to carefully insert the new target declarations instead of doing if/else. I will fix the builds as well (probably have to gut the CI infrastructure, hope you don't mind @mloskot ).

---

## Comment 9

> Username: pdimov  
> Created_at: 2021-09-18 17:28:53 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-922343604  

>I believe in the current state the library will not be usable due to missing IO dependency libraries and some builds indicate that internal boost dependencies might be missing too (align library is not in the target link libraries list but is used as include).  
  
`boostdep --cmake gil` says that the dependency list hasn't changed since I made this PR.  
  
>could you please point to a place where the expected behavior of compatible libraries is documented?  
  
Not at the moment, sorry. But the behavior basically needs to be what `boostdep --cmake gil` says.

---

## Comment 10

> Username: pdimov  
> Created_at: 2021-09-18 21:06:46 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-922373843  

What this PR does is allow `gil` to be removed from `BOOST_INCOMPATIBLE_LIBRARIES` here:  
https://github.com/boostorg/cmake/blob/4dfbec1d500598629d56e7f6b9057ec6d1115a11/include/BoostRoot.cmake#L22-L25  
which would allow it to at least be installed using CMake. It also allows its header-only use in cases the io extensions aren't needed.  
  
Doing this "properly" will be more involved. It will be easier, and more "regular", if `gil_io` is made a normal compiled library, instead of a header-only one, similar to Iostreams. This would allow it (at least in the shared lib case) to be used without needing libjpeg-devel et al.  
  
The CMakeLists file of Boost.Iostreams is a good reference example for a similar library: https://github.com/boostorg/iostreams/blob/9edd46fe730e2086675a91629287224bd92590ab/CMakeLists.txt  
  
But in either case, the general outline of `boostdep --cmake gil` needs to be followed, because any deviations are usually wrong, in various creative ways. For instance, isolating dependencies or compile options in INTERFACE subtargets, as currently done, breaks installation because these subtargets then need to be installed.  
  
At the moment, what I have as documentation is here: https://github.com/boostorg/cmake/blob/doc/developer/developer.md. It's still on a branch because it's not yet finished, but the parts that are there, are accurate. I hope it's of help.

---

## Comment 11

> Username: simmplecoder  
> Created_at: 2021-09-19 20:36:59 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-922533618  

Thank you very much for a detailed explanation and examples. @lpranam, if I  
will not be able to do in a better way, please merge what we have. I will  
make a few attempts and come back to you.  
  
On Sun, 19 Sep 2021 at 03:06 Peter Dimov ***@***.***> wrote:  
  
> What this PR does is allow gil to be removed from  
> BOOST_INCOMPATIBLE_LIBRARIES here:  
>  
> https://github.com/boostorg/cmake/blob/4dfbec1d500598629d56e7f6b9057ec6d1115a11/include/BoostRoot.cmake#L22-L25  
> which would allow it to at least be installed using CMake. It also allows  
> its header-only use in cases the io extensions aren't needed.  
>  
> Doing this "properly" will be more involved. It will be easier, and more  
> "regular", if gil_io is made a normal compiled library, instead of a  
> header-only one, similar to Iostreams. This would allow it (at least in the  
> shared lib case) to be used without needing libjpeg-devel et al.  
>  
> The CMakeLists file of Boost.Iostreams is a good reference example for a  
> similar library:  
> https://github.com/boostorg/iostreams/blob/9edd46fe730e2086675a91629287224bd92590ab/CMakeLists.txt  
>  
> But in either case, the general outline of boostdep --cmake gil needs to  
> be followed, because any deviations are usually wrong, in various creative  
> ways. For instance, isolating dependencies or compile options in INTERFACE  
> subtargets, as currently done, breaks installation because these subtargets  
> then need to be installed.  
>  
> At the moment, what I have as documentation is here:  
> https://github.com/boostorg/cmake/blob/doc/developer/developer.md. It's  
> still on a branch because it's not yet finished, but the parts that are  
> there, are accurate. I hope it's of help.  
>  
> —  
> You are receiving this because you commented.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/gil/pull/614#issuecomment-922373843>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ADGZGQC3AG3HJICON3VF3ZLUCT5PBANCNFSM46DHYUGQ>  
> .  
>

---

## Comment 12

> Username: simmplecoder  
> Created_at: 2021-09-22 19:50:38 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-925278098  

@pdimov I read through the documents and peeked at sketches of cmake infrastructure. I do not think it is possible to make IO compiled library as they are mostly templated. What do you think about this: I will duplicate the Boost.IOStreams options function ( in the CMakeLists.txt the file you linked) and if the library is not found, there will be a preprocessor directive akin to the following (this example is for JPEG include files):  
  
```  
#ifndef BOOST_GIL_JPEG_FOUND  
#warning "JPEG library was not found and was not linked, including this header is erroneous"  
#endif  
```  
  
Each IO extension header will contain the preprocessor directives and if dependency was not found, any inclusion will cause a warning. I'm not sure if it is worth escalating the `#warning` into `#error` as since it is a template, as long as it is not instantiated it will not break anything (@mloskot @sdebionne @lpranam what do you think?). This should allow us some intermediate mode while we get clearer solution sorted out. I can do this until the end of the next week if we choose to go this route.  
  
What Peter wanted was probably to make unusable headers unavailable, but due to nested structure and templates I do not think it is possible. In case of IOStreams it seems like there a common interface and the libraries are just backends (I guess they use virtual functions just like the standard streams), while in case of GIL it seems like at least major refactoring is needed in order to separate them (theoretically it should be possible to separate, but I do not have enough knowledge of the code base to say for sure).   
  
Anyway, I do not think any immediate action will achieve anything useful. If nobody else has anything to add, I guess we could merge this as is for now.

---

## Comment 13

> Username: pdimov  
> Created_at: 2021-09-22 21:05:07 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-925329179  

> and if the library is not found, there will be a preprocessor directive akin to the following  
  
Or you can just do nothing and let the user deal with linking to the library; the preprocessor warning isn't particularly useful anyway.

---

## Comment 14

> Username: mloskot  
> Created_at: 2021-10-30 21:13:55 UTC  
> Url: https://github.com/boostorg/gil/pull/614#issuecomment-955594099  

I'm going to merge this into develop and, likely, cherry pick to master.  
  
If this breaks thr old way of CMake for GIL, I'll fix it later.

---
