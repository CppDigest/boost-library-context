# #4 Use fallback implementation for aligned alloc on WinCE [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 13:17:35 UTC  
> Updated at: 2015-12-16 23:06:49 UTC  
> Closed at: 2015-12-16 23:06:49 UTC  
> Url: https://github.com/boostorg/align/pull/4  

MSVC doesn't provide aligned allocation functions for Windows CE, so don't attempt to use the ones for MSVC there.

---

## Comment 1

> Username: glenfe  
> Created_at: 2015-12-16 13:32:42 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165106739  

Thanks. Your comment explains the rationale for the `aligned_alloc.hpp` change: Why the `alignment_of.hpp` change?

---

## Comment 2

> Username: muggenhor  
> Created_at: 2015-12-16 14:04:42 UTC  
> Updated_at: 2015-12-16 14:05:29 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165114882  

I'm getting this compile error without the change to `alignment_of.hpp`:  
  
```  
boost_1_59_0\include\boost\align\detail\alignment_of_msvc.hpp(21) : error C2975: 'B' : invalid template argument for 'boost::alignment::detail::min_size', expected compile-time constant expression  
        boost_1_59_0\include\boost\align\detail\min_size.hpp(19) : see declaration of 'B'  
        boost_1_59_0\include\boost\align\detail\alignment_of_msvc.hpp(22) : see reference to class template instantiation 'boost::alignment::detail::alignment_of<T>' being compiled  
```  
  
I'm assuming it's the `offsetof` on that line that's somehow not seen as a compile-time constant. It may also be a VS2008 compiler bug, unfortunately I haven't been able to determine why exactly this fails, only that it does.

---

## Comment 3

> Username: glenfe  
> Created_at: 2015-12-16 14:39:00 UTC  
> Updated_at: 2015-12-16 14:41:41 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165128260  

Ah, that is probably because you haven't synchronized with the remote repository recently. The current `alignment_of_msvc.hpp` does not use `offsetof` because, as you've found, is only an intrinsic on newer versions of the compiler. If you git pull and it works fine (which it should) can you update the pull request to only modify `aligned_alloc.hpp`?

---

## Comment 4

> Username: muggenhor  
> Created_at: 2015-12-16 16:23:59 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165163977  

Thanks, that worked like a charm :-) I updated the pull request right away.

---

## Comment 5

> Username: glenfe  
> Created_at: 2015-12-16 17:02:57 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165175793  

Thanks! I'll hit the green merge button when I get home and run the tests at least once on my machine.

---

## Comment 6

> Username: glenfe  
> Created_at: 2015-12-16 23:06:48 UTC  
> Url: https://github.com/boostorg/align/pull/4#issuecomment-165281241  

Merged:  
  
```  
[develop fb729cf] Use fallback implementation for aligned alloc on WinCE  
 Author: Giel van Schijndel <Giel.vanSchijndel@tomtom.com>  
 Date: Thu Oct 29 15:20:50 2015 +0100  
 1 file changed, 1 insertion(+), 1 deletion(-)  
```

---
