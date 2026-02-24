# #561 Remove pch target naming restriction on GCC [Merged]

> Username: Kojoley  
> Created at: 2020-04-03 01:57:57 UTC  
> Updated at: 2021-10-02 21:13:21 UTC  
> Merged at: 2020-04-03 12:30:16 UTC  
> Closed at: 2020-04-03 12:30:16 UTC  
> Url: https://github.com/boostorg/build/pull/561  

Do not require pch target to have the same name as the header base name.  
  
The restriction was added in 76d041d7c10938adbefd6fa045231ccfa79b3fc1 without a rationale and only for GCC while reworking PCH support for GCC and MSVC.  
  
I have a use case in Spirit where I need multiple PCHs over the same header but with different macro definitions: https://github.com/boostorg/spirit/blob/390dbe2ec0324b834a29adfaed58319b909449d7/classic/test/Jamfile#L23-L25 and it is working on MSVC but fires an error on GCC.

---
