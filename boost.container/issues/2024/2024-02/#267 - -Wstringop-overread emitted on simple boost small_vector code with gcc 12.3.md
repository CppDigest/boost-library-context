# #267 - -Wstringop-overread emitted on simple boost small_vector code with gcc 12.3 [Closed]

> Username: kkumar45  
> Created at: 2024-02-29 03:44:32 UTC  
> Updated at: 2024-04-01 04:11:43 UTC  
> Closed at: 2024-04-01 04:11:43 UTC  
> Url: https://github.com/boostorg/container/issues/267  

With the recent gcc update to 12.3, the class `boost::container::small_vector` class is showing a compiler warning which is annoying for the developers when they look into the compilation log.   
   
This is acknowledged by gcc development as a bug in gcc:  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=108197  
  
The sample code can be found at: https://godbolt.org/z/TWdPjoobE  
  
I don't see a plan yet from gcc side for releasing the fix. Thus. I wanted to check if it is possible to suppress the warning in Boost temporarily? I did tried using gcc pragma push and pop for ignoring the particular warning, but since the functions are inline, it doesn't suppress it. Looking forward for any further inputs or solution.

---

## Comment 1

> Username: kkumar45  
> Created at: 2024-03-28 08:24:24 UTC  
> Url: https://github.com/boostorg/container/issues/267#issuecomment-2024653657  

Can someone take a look please?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-03-28 23:22:20 UTC  
> Url: https://github.com/boostorg/container/issues/267#issuecomment-2026307665  

I don't think there's something reasonable that Boost.Container could do, disabling the warning should be something that the user should decide. In any case, those warnings are not emitted in recent boost versions. If you take your godbolt example and change the boost version from 1.80 to the latest versions, the warning goes away. See:  
  
https://godbolt.org/z/KrM88b3bc

---

## Comment 3

> Username: kkumar45  
> Created at: 2024-04-01 04:11:43 UTC  
> Url: https://github.com/boostorg/container/issues/267#issuecomment-2029125699  

@igaztanaga yes looks like it is fixed in newer versions. thanks !
