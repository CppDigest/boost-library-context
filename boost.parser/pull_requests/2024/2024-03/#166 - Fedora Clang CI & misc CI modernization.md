# #166 Fedora Clang CI & misc CI modernization [Merged]

> Username: Jannik2099  
> Created at: 2024-03-31 00:42:55 UTC  
> Updated at: 2024-04-01 21:01:12 UTC  
> Merged at: 2024-04-01 20:58:58 UTC  
> Closed at: 2024-04-01 20:58:58 UTC  
> Url: https://github.com/boostorg/parser/pull/166  

closes #163  
  
As promised, a Fedora workflow that tests all the recent clang variants, plus the gcc that is currently available - since Fedora releases every 6 months, this'll generally be the latest gcc release. I have also added C++23 to the Fedora and Windows workflows.  
  
There are also gcc and clang trunk packages available for Fedora, but I'm not sure this'd be of interest here (and probably also cause false positives from regressions), so I've left it out.  
  
Additionally I have *taken the liberty* to enable `-Werror` for it, which is why the clang runs fail - e.g. https://github.com/Jannik2099/parser/actions/runs/8494329949/job/23269361354#step:6:147  
I haven't enabled it for the Ubuntu action nor for MSVC, since I don't know if you want to keep it.   
  
While I was at it I also modernized the other workflows - I merged the Ubuntu and Windows workflows into one respectively, using a matrix option to run on the various versions.  
  
Other misc fixes:  
Updated everything to checkout@v4  
Unified build & test invocations to `cmake --build ...` etc.  
Unified syntax, I hope  
Parallel `ctest` invocation in all workflows  
The Ubuntu workflows were still using -j2, whereas the Github runners got updated to 4 cores a while ago.  
Added `fail-fast: false` - IME it's often interesting to see which workflow variants fail, feel free to revert  
The MacOS 12 action was labeled as Clang 13, but has Clang 14 according to https://github.com/actions/runner-images/blob/main/images/macos/macos-12-Readme.md  
  
Some things I encountered:  
Clang 18 + C++20 / C++23 fails - https://github.com/Jannik2099/parser/actions/runs/8494329949/job/23269361601  
MSVC 2022 + C++23 fails - https://github.com/Jannik2099/parser/actions/runs/8494329945/job/23269360831  
You seem to be using `[[no_unique_address]]` in C++17? See https://github.com/Jannik2099/parser/actions/runs/8494329945/job/23269360254#step:4:228  
Also note that `[[no_unique_address]]` is ignored by MSVC even in C++20, you need `[[msvc::no_unique_address]]`

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-04-01 01:34:56 UTC  
> Url: https://github.com/boostorg/parser/pull/166#issuecomment-2029008387  

Thanks for doing this!  However, could you redo this against develop/HEAD?  I don't like to merge directly to master.

---

## Comment 2

> Username: Jannik2099  
> Created_at: 2024-04-01 08:27:59 UTC  
> Updated_at: 2024-04-01 10:38:17 UTC  
> Url: https://github.com/boostorg/parser/pull/166#issuecomment-2029397709  

Oops, completely overlooked that - rebased  
  
I have also fixed the remaining clang warnings (using C++17 `[[maybe_unused]]`, but that shouldn't be an issue looking at https://en.cppreference.com/w/cpp/compiler_support/17)  
  
Lastly, it seems the b2 bootstrap defaults to embedding the Windows resource file even on non-windows targets, leading to relocations that get rejected by stricter ld configurations used on some distros - I just disabled it preventatively as I don't know of any use for this anyways.  
  
EDIT: that should also probably be reported upstream, but I don't know the proper communcation channel for that

---

## Comment 3

> Username: tzlaine  
> Created_at: 2024-04-01 21:01:12 UTC  
> Url: https://github.com/boostorg/parser/pull/166#issuecomment-2030547259  

That was fast!  Thanks very much for doing this.

---
