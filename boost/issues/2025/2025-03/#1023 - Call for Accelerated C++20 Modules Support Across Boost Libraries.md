# #1023 - Call for Accelerated C++20 Modules Support Across Boost Libraries [Open]

> Username: banderzhm  
> Created at: 2025-03-24 14:35:38 UTC  
> Updated at: 2025-04-09 14:16:57 UTC  
> Url: https://github.com/boostorg/boost/issues/1023  

Dear Boost Community,  
﻿  
As the pioneer of C++ innovation, Boost has consistently led the charge in adopting groundbreaking features before they become standard. While we applaud the initial modularization efforts across several Boost libraries, the current pace of C++20 Modules adoption falls short of what the community needs and expects from this foundational project.  
﻿  
### Current Modularization Efforts (Proofs-of-Concept)  
﻿  
The community has made valuable initial progress on modularizing key components:  
﻿  
**Header-Only Libraries**  
- [Boost.Mp11](https://github.com/boostorg/mp11/pull/104) (proof-of-concept for header-only modularization)  
﻿  
**Compiled Libraries**  
- [Boost.Charconv](https://github.com/boostorg/charconv/pull/255) (proof-of-concept for compiled libraries)  
﻿  
**Core Infrastructure**  
- [Boost.Core](https://github.com/anarthal/core/tree/feature/cxx20-modules) (minimal test support subset)  
- [Boost.Assert](https://github.com/anarthal/assert/tree/feature/cxx20-modules) (minimal test support subset)  
- [Boost.ThrowException](https://github.com/anarthal/throw_exception/tree/feature/cxx20-modules) (minimal test support subset)  
- [Boost.Config](https://github.com/anarthal/config/tree/feature/cxx20-modules) (compatibility headers)  
﻿  
**Build System Support**  
- [Boost.CMake](https://github.com/anarthal/boost-cmake/tree/feature/cxx20-modules) (module installation scripts)  
﻿  
While these efforts are commendable, they represent just the first steps. The C++ ecosystem cannot afford for Boost - the standard-bearer of C++ innovation - to lag in adopting this transformative feature.  
﻿  
### Why Immediate Scaling is Critical  
﻿  
**Compiler Readiness**  
All major compilers (GCC 15, Clang 16, MSVC) now support C++20 Modules, with `import std;` already working. The toolchain is ready - where is Boost?  
﻿  
C++ has reached a historical turning point with modularization, and Boost stands at the forefront of this wave.  
﻿  
**Community Impact**  
With virtually every significant C++ project depending on Boost, the lack of comprehensive Modules support creates:  
- Unnecessary build time bottlenecks  
- Missed optimization opportunities  
- Friction in modern codebases  
﻿  
**Leadership Responsibility**  
Boost has historically bridged the gap between cutting-edge features and standardization (e.g., Filesystem, Coroutines). We must continue this tradition for Modules. Even though MSVC has some issues on the platform, both MSVC and STL are using Boost.Math, which shows the importance and necessity of Boost. And since when did one platform not being able to support something stop Boost from pushing the envelope of compilers and libraries?  
﻿  
**CMake Status and Boost's Leadership**  
CMake's C++ Modules support is now in a usable state, with no major issues. However, Boost has always held a more prominent leadership position and had a greater influence in the C++ community compared to CMake. Boost should take the lead in driving the adoption of C++20 Modules, rather than waiting for CMake to fully catch up.  
﻿  
### The Call for Boost 2  
﻿  
The idea of a Boost 2 that assumes C++20 as its starting point has been discussed for years. This would be an opportunity to shed historical baggage and embrace modern C++ fully. Such a bold move would not only align Boost with the current standards but also reinvigorate its position as the innovator in the C++ ecosystem.  
﻿  
### Suggested Next Steps  
﻿  
1. **Consider establishing a Modules Task Force**  
- This group could coordinate efforts across library maintainers  
- Document best practices for modularization  
﻿  
2. **Prioritize high-impact libraries for module conversion**  
- Focus on libraries like Boost.Asio, Boost.Compute that would benefit most from modules  
- Create reference implementations for different library types  
﻿  
3. **Enhance infrastructure support**  
- Expand CMake support for module builds  
- Enhance CI testing for modular configurations  
﻿  
4. **Increase community engagement**  
- Host regular progress updates  
- Create contribution guidelines for module conversions  
﻿  
The time for proofs-of-concept is over. The C++ community needs production-ready module support across Boost's core libraries. Without Boost leading the way, the entire ecosystem remains stalled. Let's work together to make this happen now.  
﻿  
---  
﻿  
*Reddit References*:  
- [C++20 modules and Boost: a prototype](https://www.reddit.com/r/cpp/comments/1id22se/c20_modules_and_boost_a_prototype/)  
- [GCC support std module with CMake 4.0 Now!](https://www.reddit.com/r/cpp/comments/1j7vplc/gcc_support_std_module_with_cmake_40_now/)  
- [Is it time for a rebased Boost2 that assumes C++20 as its starting point?](https://www.reddit.com/r/cpp/comments/dvt6lw/is_it_time_for_a_rebased_boost2_that_assumes_c20)  
﻿  
"Boost didn't become legendary by waiting for perfect conditions - it created them." Now is the time to lead again.

---

## Comment 1

> Username: mclow  
> Created at: 2025-03-24 14:38:43 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2748359462  

You will get a lot more visibility if you post this to the boost-developers mailing list. See https://lists.boost.org/mailman/listinfo.cgi/boost for more info.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2025-04-07 16:33:10 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2783948238  

Does this list consider the points made in the discussions on the mailing list?

---

## Comment 3

> Username: banderzhm  
> Created at: 2025-04-08 08:37:29 UTC  
> Updated at: 2025-04-08 08:38:21 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2785674077  

> Does this list consider the points made in the discussions on the mailing list?  
  
I've been following the progress of C++20 modules and working on some Boost-related modules demos. I came across this discussion thread on the Boost mailing list https://lists.boost.org/Archives/boost//2024/12/258953.php, where the lead developer of Clang modules has been communicating with the Boost community via email.  
The community has experimented with some edge modules, but progress has been stalled due to issues with using-export on the MSVC platform. The real problem is that there are very few people willing to push forward with modules that are popular and solve actual problems for a large user base.

---

## Comment 4

> Username: alandefreitas  
> Created at: 2025-04-08 16:51:37 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2787088879  

Yes. But Ruben has been working on many C++ modules for Boost, and we recently had another very long thread about modules in the mailing list. This happens every six months or so. IIRC, we concluded that the ecosystem is just not ready yet. There's not a lot of support. Once the support is there, adapting the code to support modules is not a huge deal.

---

## Comment 5

> Username: banderzhm  
> Created at: 2025-04-09 09:58:36 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2789103405  

> Yes. But Ruben has been working on many C++ modules for Boost, and we recently had another very long thread about modules in the mailing list. This happens every six months or so. IIRC, we concluded that the ecosystem is just not ready yet. There's not a lot of support. Once the support is there, adapting the code to support modules is not a huge deal.  
  
﻿  
Thanks for raising this. You're absolutely right about the ecosystem challenges, but let's not overlook that **Clang's module implementation has been production-ready since 2020** ([documented here](https://clang.llvm.org/docs/StandardCPlusPlusModules.html)). Five years in tech is practically an epoch – long enough for JavaScript frameworks to rise and fall twice over.  
﻿  
Here's the elephant in the room: **libc++/libstdc++ may have implemented `import std;`**, but where's `import boost;` for the library we _actually_ treat as C++'s extended standard library? While the official standard libraries tinker with module syntax, Boost – the backbone of modern C++ idioms from smart pointers to fiber– remains shackled to 1980s-style header hell.   
﻿  
Yes, GCC/MSVC support lags, but that's precisely why early adopters matter. Imagine the paradigm shift if **Boost starts shipping `.cppm` interfaces** alongside headers in Clang-enabled environments. Every `import boost.asio;` declaration would:  
1. Reduce template bloat in code completion engines by 60%+ (see CLion's 2023 module diagnostics)  
2. Make cross-module constexpr evaluation actually debuggable  
3. Force other compiler teams to prioritize Boost's real-world patterns over spec-checklist compliance  
﻿  
Waiting for perfect standardization risks becoming the C++ equivalent of "waiting for Godot." Let's build momentum through action rather than deliberation.   
﻿  
To your poignant question: **If not now, when?** Five years from today, do we want to be having this same debate while Rust's cargo-like tooling eats more market share? Modules are our best shot at modernizing C++'s DNA – and Boost's module implementation is the **litmus test** for whether we're serious about evolution. Let's start the transfusion where the needle already fits.  
﻿

---

## Comment 6

> Username: alandefreitas  
> Created at: 2025-04-09 14:15:55 UTC  
> Updated at: 2025-04-09 14:16:56 UTC  
> Url: https://github.com/boostorg/boost/issues/1023#issuecomment-2789894498  

Yes. The issue is the ecosystem, not a particular compiler implementation. I won't list what we discussed on the mailing list to avoid repetition, but many issues go beyond that implementation. Even `import std;` doesn't work so well yet. I agree we need `import boost;`. However, I disagree with the view that boost authors are somehow ignoring the issue. And I disagree much more with the implied proposal that we ignore all discussions and start a new one from scratch for some reason.  
  
If you want to see `import boost,` the proper way to do so is to get on the mailing list and address the issues stopping us from doing implementing it. Somehow 1) implying that everyone has been ignoring the existence of modules and 2) restating the advantages of modules we know about ad nauseum is not constructive at all. All Boost authors know exactly what C++ modules are and their advantages. Implying otherwise is close to offensive.  
  
The inclusion of Rust in the discussion is also not very helpful. It helps nothing with the technical issues we face. My personal opinion about the Rust hysteria is that if Rust is better than C++ at this stuff, then so be it. Languages are just tools. Incentivizing people to attach their self-esteem to a programming language is a bad idea. Still, I think Rust is not going very far because it promises a lot, but it can't deliver on these promises you look at the technical issues. And even if Rust could deliver on all of its promises, their "market" share is negligible. How python/javascript/swift/... is taking C++ "market" share is a much more interesting topic to me. In any case, that's my personal opinion.   
  
The point is that these personal opinions are irrelevant to the issues Boost faces in supporting modules. If we want support for modules, the solution is to sit down and fix each of the issues mentioned in the mailing list, regardless of how Rust is doing.
