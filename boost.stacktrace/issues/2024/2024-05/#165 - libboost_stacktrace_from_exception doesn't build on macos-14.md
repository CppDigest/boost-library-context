# #165 - libboost_stacktrace_from_exception doesn't build on macos-14 [Closed]

> Username: pdimov  
> Created at: 2024-05-24 02:16:34 UTC  
> Updated at: 2024-05-27 08:38:18 UTC  
> Closed at: 2024-05-27 08:38:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165  

With:  
```  
clang-darwin.compile.c++ bin.v2/libs/stacktrace/build/clang-darwin-15/release/arm_64/cxxstd-11-iso/threading-multi/visibility-hidden/from_exception.o  
libs/stacktrace/build/../src/from_exception.cpp:170:2: error: On this platform memory leaks are possible if capturing stacktrace from         exceptions is enabled and exceptions are thrown concurrently         and libc++ runtime is used.                 Define `BOOST_STACKTRACE_LIBCXX_RUNTIME_MAY_CAUSE_MEMORY_LEAK` to         suppress this error if the library would not be used with libc++         runtime (for example, it would be only used with GCC runtime).                 Otherwise, disable the boost_stacktrace_from_exception library build         (for example by `./b2 boost.stacktrace.from_exception=off` option).  
#error On this platform memory leaks are possible if capturing stacktrace from \  
 ^  
1 error generated.  
  
    "clang++"   -std=c++11 -fvisibility-inlines-hidden -fPIC -O3 -Wall -fvisibility=hidden -Wno-inline --target=arm64-apple-darwin  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DNDEBUG   -I"."  -c -o "bin.v2/libs/stacktrace/build/clang-darwin-15/release/arm_64/cxxstd-11-iso/threading-multi/visibility-hidden/from_exception.o" "libs/stacktrace/build/../src/from_exception.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/stacktrace/build/clang-darwin-15/release/arm_64/cxxstd-11-iso/threading-multi/visibility-hidden/from_exception.o...  
```  
(https://github.com/boostorg/boost/actions/runs/9216984858/job/25358236933)  
  
It's exceedingly unlikely that the library will be used with libstdc++ under macOS when built with Clang, so the default on this platform should be to not build the library.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-24 18:59:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130180037  

One easy way to fix that would be to add  
```  
<architecture>arm,<target-os>darwin,<toolset>clang:<build>no  
```  
to the `boost_stacktrace_from_exception` requirements.  
  
That's not quite right because we'd ideally want to be able to override this via `boost.stacktrace.from_exception=on`, but this isn't possible currently because `on` is the default value of this feature, so we can't know whether it was explicitly given.  
  
If we really care about that, we can change this to  
```  
feature.feature boost.stacktrace.from_exception : default on off : optional propagated ;  
```  
and then add `<boost.stacktrace.from_exception>default` to the conditions above.  
  
I'd like to have this fixed because it breaks the boostorg/boost CI at the moment, so comments are appreciated.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-24 19:14:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130207495  

Or perhaps, given that according to https://github.com/boostorg/stacktrace/issues/163 the feature never works on non-x86 by default, the right incantation would be  
```  
<build>no  
<boost.stacktrace.from_exception>on:<build>yes  
<architecture>x86:<build>yes  
```  
?  
  
Not sure. @grisumbras any comments?

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-05-24 19:38:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130244657  

Optional features aren't added to the property set by default, so if there is `<boost.stacktrace.from_exception>on` in the property set, then it was explicitly added.  
  
And wouldn't your suggestion add both `<build>no` and `<build>yes` to the property set? It should probably use a conditional property with a rule  
```jam  
rule build_from_exception( props * )  
{  
  local result = [ property.select <build> : $(props) ] ;  
  if $(result) { return $(result) ; }  
     
  local enabled = [ property.select <boost.stacktrace.from_exception> ] ;  
  switch $(enabled:G)  
  {  
    case on:   return ;  
    case off:  return <build>no;  
  }  
  
  if [ property.select <architecture> ] != x86  
  {  
    return <build>no ;  
  }  
}  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2024-05-24 19:45:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130252298  

> And wouldn't your suggestion add both `<build>no` and `<build>yes` to the property set?  
  
I thought b2 was smart enough to figure out that the more specific one of these takes precedence.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-05-24 19:52:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130259758  

Yes, I just tried  
```  
    <build>no  
    <architecture>x86:<build>yes  
    <boost.stacktrace.from_exception>on:<build>yes  
```  
and it works as I expected.  
  
This is not quite correct though because it doesn't handle `<boost.stacktrace.from_exception>off` correctly, it should be  
```  
    <build>no  
    <architecture>x86:<build>yes  
    <boost.stacktrace.from_exception>on:<build>yes  
    <architecture>x86,<boost.stacktrace.from_exception>off:<build>no  
```  
I think.

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-05-24 19:54:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130261590  

To be honest, this looks too cryptic, so I'd still choose the rule.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-05-24 19:55:08 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130262867  

Eh, with the additon, no dice  
```  
C:/boost-git/develop/tools/build/src/build\targets.jam:1133: in evaluate-requirements from module targets  
error: Can not evaluate conditional properties <architecture>x86,<boost.stacktrace.from_exception>off:<build>no <architecture>x86:<build>yes <boost.stacktrace.from_exception>on:<build>yes <conditional>@Jamfile<C:\boost-git\develop>%Jamfile<C:\boost-git\develop>.clang-darwin-cxxstd-11 <conditional>@Jamfile<C:\boost-git\develop>%Jamfile<C:\boost-git\develop>.handle-static-runtime <conditional>@Jamfile<C:\boost-git\develop>%boostcpp.deduce-address-model <conditional>@Jamfile<C:\boost-git\develop>%boostcpp.deduce-architecture <conditional>@Jamfile<C:\boost-git\develop>%threadapi-feature.detect <conditional>@object(check-target-builds-worker)@10081%check <python>2.7,<target-os>windows:<python.interpreter>C:/Python27\python <python>3.9,<target-os>windows:<python.interpreter>C:/Python39\python <target-os>linux:<library>/C:/boost-git/develop/libs/stacktrace/build//dl <toolset>como-linux:<define>_GNU_SOURCE=1 <toolset>como:<link>static <toolset>msvc,<runtime-link>shared:<threading>multi  
```

---

## Comment 8

> Username: pdimov  
> Created at: 2024-05-24 19:55:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130264439  

The rule is way more cryptic to me, but I don't care one way or the other, as long as it works.

---

## Comment 9

> Username: pdimov  
> Created at: 2024-05-24 19:56:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130267027  

Have I mentioned that each time I encounter an optional property, it doesn't quite work?

---

## Comment 10

> Username: pdimov  
> Created at: 2024-05-24 20:06:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130283187  

Your rule, after I fix the seven or so syntax errors, doesn't seem to work.

---

## Comment 11

> Username: grisumbras  
> Created at: 2024-05-24 20:22:48 UTC  
> Updated at: 2024-05-25 05:38:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130302661  

Fixed version:  
  
```jam  
import property ;  
rule build-from-exception ( props * )  
{  
    local result = [ property.select <build> : $(props) ] ;  
    if $(result) { return $(result) ; }  
  
    local enabled = [ property.select <boost.stacktrace.from_exception> : $(props) ] ;  
    switch $(enabled:G=)  
    {  
        case "on" :  return ;  
        case off  :  return <build>no ;  
    }  
  
    local arch = [ property.select <architecture> : $(props) ] ;  
    if $(arch) && ( $(arch) = x86 )  
    {  
        return <build>no ;  
    }  
}  
```

---

## Comment 12

> Username: pdimov  
> Created at: 2024-05-25 01:53:05 UTC  
> Updated at: 2024-05-25 01:53:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130658093  

I don't think this works either; we want `<build>no` when the architecture isn't x86, not when it is.  
  
And separately, when I tried a fixed version of your previous suggestion, `b2 build boost.stacktrace.from_exception=on` didn't build the library for some reason.

---

## Comment 13

> Username: grisumbras  
> Created at: 2024-05-25 06:34:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130912168  

The reason my original rule didn't work is exactly because it returned `<build>no`  when `<architecture>` was not in the property set. It went like this:  
1. My rule is evaluated, it returns `<build>no` because no `<architecture>`.  
2. `boostcpp.deduce-architecture` is evaluated, it returns `<architecture>x86`.  
3. My rule is evaluated again with the updated property set. The property set has `<build>no` from step 1, so that's what it is returning.  
  
The fixed rule in step 1 doesn't return `<build>no` if no `<architecture>`, so on step 3 it can see that the architecure is in fact x86.  
  
Since all targets in Boost tree have `<conditional>@boostcpp.deduce-architecture`, this will work for all deducible architectures. For non-deducible architectures I don't have a solution, I'm afraid.  
  
Well, I do: make a config check:  
```jam  
import property ;  
import configure ;  
import boostcpp ;  
rule build-from-exception ( props * )  
{  
    local enabled = [ property.select <boost.stacktrace.from_exception> : $(props) ] ;  
    switch $(enabled:G=)  
    {  
        case "on" :  return ;  
        case off  :  return <build>no ;  
    }  
  
    local arch = [ property.select <architecture> : $(props) ] ;  
    if $(arch) && ( $(arch) = x86 )  
    {  
        return ;  
    }  
  
    local filtered = [ boostcpp.toolset-properties $(props) ] ;  
    local deduced = [ configure.find-builds "default architecture" : $(filtered)  
        : /boost/architecture//x86 ] ;  
    if $(deduced)  
    {  
        return ;  
    }  
  
    return <build>no ;  
}  
```  
  
Do we want to go there?

---

## Comment 14

> Username: pdimov  
> Created at: 2024-05-25 11:04:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131214582  

Why didn't you post a working version of the previous rule? Is it  
```  
rule build-from-exception ( props * )  
{  
    local result = [ property.select <build> : $(props) ] ;  
    if $(result) { return $(result) ; }  
  
    local enabled = [ property.select <boost.stacktrace.from_exception> : $(props) ] ;  
    switch $(enabled:G=)  
    {  
        case "on" :  return ;  
        case off  :  return <build>no ;  
    }  
  
    local arch = [ property.select <architecture> : $(props) ] ;  
    if $(arch) && ( $(arch) = x86 )  
    {  
        return ;  
    }  
  
    return <build>no ;  
}  
```  
?

---

## Comment 15

> Username: pdimov  
> Created at: 2024-05-25 11:12:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131218722  

No, that doesn't work either :-)

---

## Comment 16

> Username: grisumbras  
> Created at: 2024-05-25 11:20:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131220844  

> Why didn't you post a working version of the previous rule?  
  
https://github.com/boostorg/stacktrace/issues/165#issuecomment-2130302661 worked for me. Note, that I edited it after posting (it had a typo).

---

## Comment 17

> Username: pdimov  
> Created at: 2024-05-25 11:24:43 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131221769  

I don't see how  
```  
    local arch = [ property.select <architecture> : $(props) ] ;  
    if $(arch) && ( $(arch) = x86 )  
    {  
        return <build>no ;  
    }  
```  
can possibly work, since it checks the wrong condition (`=` instead of `!=`).  
  
We want to build when the architecture is x86, not when it isn't.  
  
It "worked" because `= x86` is never true because of the missing `:G=`.  
  
This one seems to work for me, although I'm not sure how to test it for non-x86:  
  
```  
rule build-stacktrace-from-exception ( props * )  
{  
    local build = [ property.select <build> : $(props) ] ;  
    local enabled = [ property.select <boost.stacktrace.from_exception> : $(props) ] ;  
    local arch = [ property.select <architecture> : $(props) ] ;  
  
    if $(build)  
    {  
        return $(build) ;  
    }  
  
    switch $(enabled:G=)  
    {  
        case  "on" :  return ;  
        case "off" :  return <build>no ;  
    }  
  
    if $(arch) && ( $(arch:G=) != x86 )  
    {  
        return <build>no ;  
    }  
}  
```

---

## Comment 18

> Username: grisumbras  
> Created at: 2024-05-25 13:58:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131282595  

Sorry for the garbage I posted before. This one I tested with  
`b2 libs/stacktrace/build` (builds), `b2 libs/stacktrace/build boost.stacktrace.from_exception=on` (builds), `b2 libs/stacktrace/build boost.stacktrace.from_exception=off` (doesn't build), `b2 libs/stacktrace/build architecture=arm` (doesn't build), `b2 libs/stacktrace/build architecture=arm boost.stacktrace.from_exception=off` (doesn't build), and  `b2 libs/stacktrace/build architecture=arm boost.stacktrace.from_exception=on` (tries to build, but fails as expected).  
  
```jam  
import property ;  
rule build-from-exception ( props * )  
{  
    local enabled = [ property.select <boost.stacktrace.from_exception> : $(props) ] ;  
    switch $(enabled:G=)  
    {  
        case "on" :  return ;  
        case off  :  return <build>no ;  
    }  
  
    local arch = [ property.select <architecture> : $(props) ] ;  
    if $(arch) && ( $(arch:G=) != x86 )  
    {  
        return <build>no ;  
    }  
}  
```  
  
It's basically what you posted, but I removed checking for `<build>`, as it is unnecessary.

---

## Comment 19

> Username: pdimov  
> Created at: 2024-05-25 14:24:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/165#issuecomment-2131289529  

PR created: https://github.com/boostorg/stacktrace/pull/166.
