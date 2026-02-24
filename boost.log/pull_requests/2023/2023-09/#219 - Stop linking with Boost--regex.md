# #219 Stop linking with Boost::regex. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2023-09-25 14:58:51 UTC  
> Updated at: 2023-09-26 01:11:02 UTC  
> Closed at: 2023-09-26 01:11:02 UTC  
> Url: https://github.com/boostorg/log/pull/219  

Normally Boost regex is header only when using C++ >= 11, and C++11 become mandatory in Boost::log recently.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2023-09-25 15:00:03 UTC  
> Url: https://github.com/boostorg/log/pull/219#issuecomment-1733897261  

Note: for now I didn't test this locally yet, I need to integrate this in my own build of Boost (we use internally boost 1.83, where C++11 was not mandatory yet). The main reason for opening the pull request at this point is to run your CI to see how things are broken with these changes (hopefully it's not, but I prefer having a CI double check !)

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-09-25 15:12:47 UTC  
> Url: https://github.com/boostorg/log/pull/219#issuecomment-1733922870  

I don't think Boost.Regex requires the same level of C++11 for its header-only version as Boost.Log does. In particular, I see it [requires](https://github.com/boostorg/regex/blob/237e69caf65906d0313c9b852541b07fa84a99c1/include/boost/regex/config.hpp#L100) `BOOST_NO_CXX11_HDR_TYPE_TRAITS`, which is only supported since gcc 5, and Boost.Log currently supports versions older than that.

---

## Comment 3

> Username: Lastique  
> Created_at: 2023-09-25 15:15:46 UTC  
> Updated_at: 2023-09-25 15:16:26 UTC  
> Url: https://github.com/boostorg/log/pull/219#issuecomment-1733928370  

I think, I could merge a change to stop linking with Boost.Regex, but only if it is confirmed by a configure check. That is, the configure time test program should verify if Boost.Regex is header-only for the current build. But I don't think I'll merge the unconditional drop of linking.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2023-09-25 16:02:20 UTC  
> Url: https://github.com/boostorg/log/pull/219#issuecomment-1734043507  

Mmmh at this point I have no idea how to write a configure check (practically speaking, I don't know anything about the boost build system right now, but I can learn). But even if I knew this, honestly, I don't really know how I would check if boost regex is header only or not for the current toolset. If you build a basic .cpp file trying to use boost log headers, but not boost log lib, and try to link this, in the end you only checked that this very specific .cpp file doesn't need the log library, but technically other C++ code might need it. So in the end, it would only be an allow list (if the toolset is gcc, then if it's >= X.Y.Z it's header only, otherwise not, if the toolset is clang then...). And I am not sure this is something you want to maintain (if I were a maintainer, I wouldn't like this much to be honest).  
  
So, what's your opinion here ?

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2023-09-25 16:04:06 UTC  
> Url: https://github.com/boostorg/log/pull/219#issuecomment-1734046437  

I am just thinking about something else.  
  
Is there a way to detect the C++ dialect used to build boost itself, at build time ? If yes, would you be ok to define some threshold, like: "if boost itself is built with C++ >= 14 or 17, then I assume the underlying compiler is recent enough and we consider Log is header only, otherwise no" ?

---
