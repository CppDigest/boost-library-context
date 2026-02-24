# #44 Fix C4250 warning because of VS2012 CRT [Merged]

> Username: Kojoley  
> Created at: 2017-12-09 18:46:25 UTC  
> Updated at: 2017-12-11 10:24:37 UTC  
> Merged at: 2017-12-11 00:59:48 UTC  
> Closed at: 2017-12-11 00:59:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44  

The workaround in VC++ CRT of MSVC compiler bug leads to the fact that if your class inherits from std streams you will get C4250 warning. (https://connect.microsoft.com/VisualStudio/feedback/details/733720/)  
  
```  
..\..\..\..\boost/iostreams/stream.hpp(145) : warning C4250: 'boost::iostreams::stream<Device>' : inherits 'std::basic_istream<_Elem,_Traits>::std::basic_istream<_Elem,_Traits>::_Add_vtordisp1' via dominance  
..\..\..\..\boost/iostreams/stream.hpp(145) : warning C4250: 'boost::iostreams::stream<Device>' : inherits 'std::basic_ostream<_Elem,_Traits>::std::basic_ostream<_Elem,_Traits>::_Add_vtordisp2' via dominance  
```  
  
If you are not fine with this fix, will it be okay to suppress it like that https://github.com/Kojoley/iostreams/commit/ba61a0c1c376e89617a09ff171964a317bb7f236?

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-12-10 01:22:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350518090  

This is not something I reallywant to do just to suppress a warning, especially when the warning is benign and the fix is arcane. I would rather use the #pragma to suppress the warning for the particlar vc++ version in which it occurs.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-10 10:38:31 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350539118  

I've forcepushed the altfix.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-12-10 22:44:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350587915  

Can we not place the pragams around just around the boost::iostreams::stream template rather than around everything construct in the file ? Also is this necessary for just VS2012 or is it necessary for other previous versions also, in which case specifying _MSC_VER <= 1700 would be more appropriate. Was this fixed by Microsoft in VS2013 on up ?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2017-12-10 23:05:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350589244  

> Can we not place the pragams around just around the boost::iostreams::stream template rather than around everything construct in the file ?  
  
We have to place it around `stream_base` and `stream`, I can place the pragma lower after the `stream_traits` or make two ifdefs around both classes if you wish.  
The smallest scope that will work is https://github.com/Kojoley/iostreams/commit/956c067b12bfd3ba2988b49fceadb59f3ec10a36.  
  
> Also is this necessary for just VS2012 or is it necessary for other previous versions also, in which case specifying _MSC_VER <= 1700 would be more appropriate. Was this fixed by Microsoft in VS2013 on up ?  
  
It is only VS2012 problem, look at iostreams regression matrix http://www.boost.org/development/tests/develop/developer/iostreams.html  
They removed `_Add_vtordisp1` and `_Add_vtordisp2` dummies in VS2013 because they have added a new pragma `vtordisp` to deal with the root problem.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-12-11 00:13:55 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350594039  

> We have to place it around stream_base and stream, I can place the pragma lower after the stream_traits or make two ifdefs around both classes if you wish.  
The smallest scope that will work is Kojoley/iostreams@956c067.  
  
Yes I would like to see this with two separate ifdefs around the stream_base and stream templates.

---

## Comment 6

> Username: Kojoley  
> Created_at: 2017-12-11 00:19:42 UTC  
> Url: https://github.com/boostorg/iostreams/pull/44#issuecomment-350594500  

Done.

---
