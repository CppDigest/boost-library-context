# #132 Add support for Visual Studio 15 [Merged]

> Username: MarcelRaad  
> Created at: 2016-07-03 20:59:08 UTC  
> Updated at: 2021-10-02 22:08:33 UTC  
> Merged at: 2016-07-13 06:44:08 UTC  
> Closed at: 2016-07-13 06:44:08 UTC  
> Url: https://github.com/boostorg/build/pull/132  

Tested with Visual Studio 15 Preview 2. The compiler itself is the same as with Visual Studio 2015, but if only Visual Studio 15 is installed, the compiler could not be found.

---

## Comment 1

> Username: jhunold  
> Created_at: 2016-07-08 13:04:40 UTC  
> Url: https://github.com/boostorg/build/pull/132#issuecomment-231353960  

Something is wrong with this patch. The compiler version for "Visual Studio 2015" is "vc14" and supported for a while. Building with only "Visual Studio 2015" installed works for me.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-07-08 19:54:36 UTC  
> Url: https://github.com/boostorg/build/pull/132#issuecomment-231456694  

@jhunold This is not for VS 14 (2015), but for VS 15 (2017?). It currently comes with the same compiler as VS 14 in the Preview, but if you only have VS 15 installed, building Boost is currently not possible.  
  
If you're confused by the comment in build.jam, it's above the vc14 section.

---

## Comment 3

> Username: jhunold  
> Created_at: 2016-07-12 15:20:16 UTC  
> Url: https://github.com/boostorg/build/pull/132#issuecomment-232081857  

Then please update the commit message accordingly. Something along the lines of "Add support for upcoming Visual Studio 2016(/17)" and "Tested with Visual Studio 16(/17) Preview 2."   
We have to distinguish between the IDE (Visual Studio 2016(/17)) and the compiler (vc15) here.   
It would be best if you can figure out the final IDE version as I'm not following the msvc development that closely.  
Otherwise the patch looks good to me.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2016-07-12 20:20:06 UTC  
> Url: https://github.com/boostorg/build/pull/132#issuecomment-232167514  

Thanks! I've changed the commit message. Microsoft themselves always call it Visual Studio "15" Preview. I'm still hoping they'll finally get rid of the double numbering with version number and year and just call it VS 15.

---
