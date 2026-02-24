# #848 Docs: detect version of clang++ [Closed]

> Username: sdarwin  
> Created at: 2024-07-10 13:06:58 UTC  
> Updated at: 2024-07-11 06:22:35 UTC  
> Closed at: 2024-07-11 06:22:35 UTC  
> Url: https://github.com/boostorg/url/pull/848  

@alandefreitas wrote: "@sdarwin The Antora workflow that generates the preview will probably break again." Yes the same issue occurred on http_proto.  This fix allows `build_antora.sh` to be run locally, or from another location, not depending on inputs from CI.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-07-10 13:18:36 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2220491690  

An automated preview of the documentation is available at [https://848.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://848.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-07-10 13:32:51 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2220523473  

GCOVR code coverage report [https://848.url.prtest2.cppalliance.org/gcovr/index.html](https://848.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://848.url.prtest2.cppalliance.org/genhtml/index.html](https://848.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://848.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://848.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-07-10 13:32:52 UTC  
> Updated_at: 2024-07-10 13:34:16 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2220523532  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/848?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`951477d`)](https://app.codecov.io/gh/boostorg/url/commit/951477ddf77af8489500278b5d1db956fbd879ad?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`2736b6f`)](https://app.codecov.io/gh/boostorg/url/commit/2736b6fb8885950a8a6b14805b82227260fc6734?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/848/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/848?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #848   +/-   ##  
========================================  
  Coverage    99.21%   99.21%             
========================================  
  Files          157      157             
  Lines         8422     8422             
========================================  
  Hits          8356     8356             
  Misses          66       66             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/848?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/848?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [951477d...2736b6f](https://app.codecov.io/gh/boostorg/url/pull/848?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 4 [Commented]

> Username: alandefreitas  
> Created_at: 2024-07-10 16:07:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/848#pullrequestreview-2169646077  

The biggest issue with my initial comment is whether clang++-18 is actually available on the machine that builds the preview. Is it?

📁 doc/build_antora.sh

```diff
  11 |+ 
  12 |+ # If CXX was not already set, then determine the newest clang.
  13 |+ if [ ! -n "$CXX" ]; then
```

> Username: alandefreitas  
> Created_at: 2024-07-10 16:06:20 UTC  
> Updated_at: 2024-07-10 16:07:21 UTC  
> Url: https://github.com/boostorg/url/pull/848#discussion_r1672592382  

The workflow already sets CXX and CC. Is this change to ensure it works in the container that builds the preview?


---

## Comment 5

> Username: alandefreitas  
> Created_at: 2024-07-10 16:08:49 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2220931434  

Maybe I wasn't explicit enough in my initial comment but https://github.com/boostorg/url/pull/847 already addressed the environment variables among other things in https://github.com/boostorg/url/commit/95e50b8c8e7b7ce094bf33f24256819966634732

---

## Comment 6

> Username: sdarwin  
> Created_at: 2024-07-10 16:24:57 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2220964011  

Yes, in the container that builds the previews.     
A script that performs some function, let's say "builds the docs with antora", should ideally work "in the wild", whether that is Jenkins previews, or if a developer is running it locally on their laptop to build docs.   In those cases, the operating system might have both gcc and clang installed.  `build_antora.sh` needs to prefer clang. This code causes clang to be chosen. But allows overrides as well. CXX may be specified beforehand.  
  
Another topic.. this section:  
  
```  
      - name: Build Antora Docs  
        run: |  
          cd doc  
          bash ./build_antora.sh  
            
          # Antora returns zero even if it fails, so we check if the site directory exists  
          if [ ! -d "build/site" ]; then  
              echo "Antora build failed"  
              exit 1  
          fi  
```  
            
 Can be simplified to :  
   
```  
       - name: Build Antora Docs  
        run: |  
          set -e  
          cd doc  
          bash ./build_antora.sh  
```  
           
If `build_antora.sh` also uses `set -e`.   Generally a nice idea to add in any multi-line bash scripts to discover hidden errors.

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2024-07-10 21:02:38 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2221461549  

> Yes, in the container that builds the previews.  
  
Nice :)  
  
> A script that performs some function, let's say "builds the docs with antora", should ideally work "in the wild", whether that is Jenkins previews, or if a developer is running it locally on their laptop to build docs.   
  
Yes. I opened an issue in MrDocs to address that: https://github.com/cppalliance/mrdocs/issues/644  
  
When this changes, any compiler can run cmake to generate the compile_commands.json, but the standard library headers will ultimately come from MrDocs so there will never be any conflict between the mrdocs compiler and the standard library headers.  
  
> In those cases, the operating system might have both gcc and clang installed. build_antora.sh needs to prefer clang.   
  
Yes. This logic is already in the javascript script that generates the reference pages. It prefers clang, then GCC, than MSVC.  
It only falls back to `process.env.CXX_COMPILER` or `process.env.CXX` when the compiler is not found.  
  
https://github.com/boostorg/url/blob/95e50b8c8e7b7ce094bf33f24256819966634732/doc/generate-files.mjs#L98  
  
The problem we have is it fails to find `clang++` because installing in ubuntu `clang++-18` only gives us the `clang++-18` executable. Not `clang++`. I will address that.  
  
> This code causes clang to be chosen. But allows overrides as well. CXX may be specified beforehand.  
  
Yes. That's also the case because the script that generates the reference pages also checks these environment variables. The problem for now is more about clang++-18 not being available or when it can't be found because CXX is not set. But it already identifies CXX.  
  
We should be good as long as clang++-18 is available. I'll include some logic to look for clang++-\d+ and gcc-\d+ in the extension.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2024-07-10 22:06:38 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2221609235  

It sounds like you will solve the problem in various other (better) ways within Mr. Docs itself, and so on.  OK.  
  
> We should be good as long as clang++-18 is available.   
  
The previews container has clang++-18.  
  
 But was getting `error: definition of builtin function '_mm_setcsr'`.  
  
The logic in this pull request searches for the newest clang, rather than specifically 18. Soon it will happen that clang-20 is available while clang-18 is missing.  That case would already be handled.    But if you will solve the problem more correctly elsewhere, that is great.

---

## Comment 9

> Username: alandefreitas  
> Created_at: 2024-07-10 22:26:21 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2221630893  

> It sounds like you will solve the problem in various other (better) ways within Mr. Docs itself and so on. OK.  
  
Yes. There are many levels to it.   
  
We had this problem finding the compiler, which I was already fixing in the Antora extension, so it finds these latest versioned executables by itself.  
  
MrDocs currently needs the compiler to run cmake, but there's potential for improvement. In the future, MrDocs might be able to use itself as the compiler.   
  
However, the other issue is with the standard library, for which we also have an issue in the MrDocs repo.  
  
> The previews container has clang++-18.  
> But was getting error: definition of builtin function '_mm_setcsr'.  
  
Nice. That's the only thing I needed clarification on. As long as the compiler is available, https://github.com/boostorg/url/pull/843 should solve the issue.   
  
The PR is already receiving previews again: https://843.urlantora.prtest2.cppalliance.org/site/index.html  
  
> The logic in this pull request searches for the newest clang, rather than specifically 18. Soon it will happen that clang-20 is available while clang-18 is missing. That case would already be handled. But if you will solve the problem more correctly elsewhere, that is great.  
  
Yes. The solution implemented in the extension is a little more elegant because it's at a lower level and because in bash we would "need" to look for clang-40, clang-39, ...

---

## Comment 10

> Username: alandefreitas  
> Created_at: 2024-07-11 06:22:35 UTC  
> Url: https://github.com/boostorg/url/pull/848#issuecomment-2222126900  

Obsoleted by #843

---
