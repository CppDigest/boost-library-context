# #89 Conditionally replace deprecated/removed C++98 std::bind1st by std::b… [Merged]

> Username: DanielaE  
> Created at: 2017-05-10 13:47:31 UTC  
> Updated at: 2018-01-02 15:16:07 UTC  
> Merged at: 2017-05-26 21:04:35 UTC  
> Closed at: 2017-05-26 21:04:35 UTC  
> Url: https://github.com/boostorg/graph/pull/89  

…ind, std::auto_ptr by std::unique_ptr, and std::random_shuffle by std::shuffle. Inline typedefs from C++98 function adapters.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: murraycu  
> Created_at: 2017-05-11 10:22:28 UTC  
> Url: https://github.com/boostorg/graph/pull/89#issuecomment-300748251  

(I am not the maintainer.) There is lots of code in BGL that should be updated to C++11. I wouldn't want lots of ifdefs in the code. I wish boost would just choose to target newer compiler versions so it can move forward properly. C++11 is not too new.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-05-11 14:43:09 UTC  
> Url: https://github.com/boostorg/graph/pull/89#issuecomment-300811209  

Yeah!  
Unfortunately, as long as Boost.Graph supports pre-C++11 compilers, I have to deal with the fact that new versions of msvc (in C++17 mode) no longer support the long-deprecated C++98 stuff that is removed from the C++17 standard.

---
