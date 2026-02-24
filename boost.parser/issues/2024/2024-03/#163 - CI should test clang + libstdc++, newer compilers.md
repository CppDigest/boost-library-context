# #163 - CI should test clang + libstdc++, newer compilers [Closed]

> Username: Jannik2099  
> Created at: 2024-03-15 23:13:22 UTC  
> Updated at: 2024-04-01 20:58:59 UTC  
> Closed at: 2024-04-01 20:58:59 UTC  
> Url: https://github.com/boostorg/parser/issues/163  

libstdc++ is the STL used on most linux targets, and clang will default to libstdc++ on practically all of them.  
  
At the same time, incompatibilities may be more common particularly around new language features, as libstdc++ is developed against the language support of g++ foremost - see e.g. https://github.com/llvm/llvm-project/issues/54052 for a recent example.  
  
I would propose adding a new CI workflow based on an up to date distro (my goto for this is fedora) to test the latest gcc and clang versions - the stock Ubuntu image offered by Github is and will always be outdated since it's on a 2 year LTS schedule.  
  
Is that roughly to your liking? I would work on a PR if so.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-03-30 18:41:58 UTC  
> Url: https://github.com/boostorg/parser/issues/163#issuecomment-2028438800  

I regularly test on Linux + GCC 13 and Linux + GCC 12 locally; this is how all the development occurs.  CI handles older GCCs.  I definitely would like to have coverage for Clang 15 and Clang 16, on Linux and elsewhere.  So yes, that would be very much to my liking. :)  I am currently using Github actions and Drone testing provided by the C++ Alliance to Boost repos, and neither one covers recent Clangs as far as I can tell.
