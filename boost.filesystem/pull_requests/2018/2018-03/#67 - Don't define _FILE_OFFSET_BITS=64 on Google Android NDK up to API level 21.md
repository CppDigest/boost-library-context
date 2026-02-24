# #67 Don't define _FILE_OFFSET_BITS=64 on Google Android NDK up to API level 21 [Closed]

> Username: Lastique  
> Created at: 2018-03-01 23:08:48 UTC  
> Updated at: 2018-03-03 18:40:32 UTC  
> Closed at: 2018-03-03 18:40:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67  

The newer Google NDK versions don't declare truncate() function when  
`_FILE_OFFSET_BITS=64` is defined. Don't define that macro and fall back to 32-bit  
offsets.  
  
Should fix https://github.com/boostorg/filesystem/issues/65

---

## Comment 1

> Username: Bjoe  
> Created_at: 2018-03-02 13:02:59 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67#issuecomment-369915253  

Hi. @Lastique looks like my patch file [android-boost-1_65_1-patches.patch.txt](https://github.com/boostorg/filesystem/files/1538797/android-boost-1_65_1-patches.patch.txt) and as I explained [on 7 Dec 2017 (see first message)](https://github.com/boostorg/filesystem/issues/65#issue-280092873) before google add the "unified headers solution", it was silently ignore the define of _FILE_OFFSET_BITS=64 in lower API level then 21 and uses truncate() instate of truncate64(). This is really worst! Now we can see at compile time that feature/function xy like truncate64() is not supported by this API level. This is really an improvement! So I think this statement "...NDK is broken though as it doesn't provide truncate() ..." is not a fairly comment ;-) ... its not supporetd in lower API levels...  
Anyways... the real question and thats why I didn't create a pull request, it fixes the compile issue but it is not something I will add in the production code or? What happens if boost::filesystem::resize_file() should resize a large file with truncate() instate of truncate64() on android? Maybe the filesystem is getting corrupted...  
I think the correct solution is to improve the BOOST_RESIZE_FILE macro and not silently ignore the fact that the android NDK lower then API 21 still not supported truncate64(). But as I written [on 7 Dec 2017 (see first message)](https://github.com/boostorg/filesystem/issues/65#issue-280092873) ... I still waiting for some contributors.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-02 14:00:13 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67#issuecomment-369927946  

I don't see how one can resize a file to more than 2 GiB on a system that does not support 64-bit file offsets.  
  
Regarding the "broken" comment, I understand the rationale behind the current behavior of NDK, but I still don't think it is correct. NDK should really not support `_FILE_OFFSET_BITS=64` if it can't provide the full support for it. I will change the comment, though, if the library maintainer asks.

---

## Comment 3

> Username: Lastique  
> Created_at: 2018-03-02 14:38:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67#issuecomment-369937974  

I've added a check for whether the size argument to `resize_file` fits in the `off_t` value.

---

## Comment 4

> Username: rcdailey  
> Created_at: 2018-03-02 21:32:49 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67#issuecomment-370058829  

I verified this fixes my specific issue over on #65. Can we get this merged and put into a hotfix for 1.66?

---

## Comment 5

> Username: Lastique  
> Created_at: 2018-03-03 18:40:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/67#issuecomment-370169968  

Closing in favor of https://github.com/boostorg/filesystem/pull/69.

---
