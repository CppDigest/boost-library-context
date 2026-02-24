# #283 Fix ABI detection for empty 'os.platform' [Merged]

> Username: iv-m  
> Created at: 2025-01-23 13:05:36 UTC  
> Updated at: 2025-01-24 04:47:12 UTC  
> Merged at: 2025-01-24 04:47:12 UTC  
> Closed at: 2025-01-24 04:47:12 UTC  
> Url: https://github.com/boostorg/context/pull/283  

`in` operator in bjam always returns true if its first argument has no elements[1]. This means that if `os.platform` is empty (not detected), the construction introduced in commit 9084161b8778c18ebce7514f886f0f22f9bff490 sets ABI to `aapcs` on all platforms where `os.platform` is empty, including, e.g. loongarch64, and breaks build there.  
  
This commit refactors the condition to use '=' operator, to make sure that when `os.platform` is empty we get the default ABI value, and thus fixes build on loongarch64.  
  
See also 819c2d6423b4e0f55c5f69e334bf81570e82f68f for similar fix that was somehow lost in refactorings. This time a comment is added near the condition in hope that future edits will not reintroduce the issue.  
  
[1] https://www.boost.org/doc/libs/1_83_0/tools/build/doc/html/index.html#jam.language.flow_of_control  
  
Fixes: 9084161b8778c18ebce7514f886f0f22f9bff490

---

## Comment 1

> Username: iv-m  
> Created_at: 2025-01-23 13:09:10 UTC  
> Url: https://github.com/boostorg/context/pull/283#issuecomment-2609769366  

I faced this issue when building boost 1.87.0 on loongarch64. The b2 output and fix are similar to #234:  
  
`error: No best alternative for /usr/src/RPM/BUILD/boost-1.87.0/libs/context/build/asm_sources with <abi>aapcs <address-model>64 <architecture>loongarch <asynch-exceptions>off <binary-format>elf <boost.beast.allow-deprecated>on <boost.beast.separate-compilation>on <boost.cobalt.executor>any_io_executor <boost.cobalt.pmr>std <boost.process.fs>boost <context-impl>fcontext <coverage>off <debug-symbols>off <exception-handling>on <extern-c-nothrow>off <inlining>full <known-warnings>hide <link>shared <optimization>off <os>LINUX <pch>off <preserve-test-targets>on <profiling>off <python-debugging>off <python>3.12 <relevant>abi <relevant>address-model <relevant>architecture <relevant>binary-format <relevant>toolset <rtti>on <runtime-debugging>off <runtime-link>shared <stdlib>native <strip>off <target-os>linux <testing.execute>on <threadapi>pthread <threading>multi <toolset-gcc:version>14 <toolset>gcc <variant>release <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>on <x-deduced-platform>loongarch_64`

---

## Comment 2

> Username: olk  
> Created_at: 2025-01-24 04:47:08 UTC  
> Url: https://github.com/boostorg/context/pull/283#issuecomment-2611575297  

ty

---
