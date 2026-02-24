# #219 - Suggestion: Easier orientation for newbies [Open]

> Username: nigels-com  
> Created at: 2024-07-24 12:43:43 UTC  
> Updated at: 2024-07-24 13:08:15 UTC  
> Url: https://github.com/boostorg/charconv/issues/219  

Hello, I came here via the boost mailing list, out of curiosity about the upcoming boost release.  
  
Even though I work on mostly C++20 code bases (how good is that!) I didn't happen to be familar with [C++ 17 charconv](https://en.cppreference.com/w/cpp/header/charconv) and it look me to little while to figure out the scope and possible use of boost::charconv.   Years ago I got interested in a C++ alternative to iostreams and printf, so there are details here that in indeed familiar, so that helped the penny drop.   Definitely the unit tests shed a lot more light for me.  
  
Performance is a recurring issue in the work I do.  If boost::charconv (or indeed C++17 charconv) can help me read and write huge CSV files faster, that would be interesting indeed.  Generally I use [fmt::format](https://fmt.dev/11.0/) as my goto, certainly some performance characterisation would be of interest.  
  
I do appreciate that as developers we tend to focus on the implementation details, and perhaps less on the marketing.  
  
Anyway, best wishes with charconv in boost 1.86.0!

---

## Comment 1

> Username: nigels-com  
> Created at: 2024-07-24 12:46:39 UTC  
> Url: https://github.com/boostorg/charconv/issues/219#issuecomment-2247826839  

Second question, if I'm already in C++20 do I still need this?  
  
For example we use boost for some things where we want consistency between Windows and Linux, such as random numbers.  Often we don't actually care about the wiggle-room in the standards we just want consistency across platforms.

---

## Comment 2

> Username: mborland  
> Created at: 2024-07-24 13:08:14 UTC  
> Url: https://github.com/boostorg/charconv/issues/219#issuecomment-2247881047  

> Hello, I came here via the boost mailing list, out of curiosity about the upcoming boost release.  
>   
> Even though I work on mostly C++20 code bases (how good is that!) I didn't happen to be familar with [C++ 17 charconv](https://en.cppreference.com/w/cpp/header/charconv) and it look me to little while to figure out the scope and possible use of boost::charconv. Years ago I got interested in a C++ alternative to iostreams and printf, so there are details here that in indeed familiar, so that helped the penny drop. Definitely the unit tests shed a lot more light for me.  
>   
> Performance is a recurring issue in the work I do. If boost::charconv (or indeed C++17 charconv) can help me read and write huge CSV files faster, that would be interesting indeed. Generally I use [fmt::format](https://fmt.dev/11.0/) as my goto, certainly some performance characterisation would be of interest.  
>   
  
There's no mechanism to filter out the commas in the CSV but if you pre-parsed and then you can offloaded numeric conversion to charconv. I believe that `std::format` uses `<charconv>` in the background so you may see little benefit.   
  
> I do appreciate that as developers we tend to focus on the implementation details, and perhaps less on the marketing.  
>   
> Anyway, best wishes with charconv in boost 1.86.0!  
  
Thank you!  
  
Do you have any particular suggestions on what would have made it easier for you now that you have a grasp on what the library is?   
  
>Second question, if I'm already in C++20 do I still need this?  
>For example we use boost for some things where we want consistency between Windows and Linux, such as random >numbers. Often we don't actually care about the wiggle-room in the standards we just want consistency across platforms.  
  
Depends on your tool chain. Clang doesn't support floating-point operations (I believe it will in 19+), GCC requires 11+, and MSVC requires 19.24+. The standard is pretty strict on numeric conversions so you won't see any real difference in output other than the handling of the different types and signs on `NAN`.
