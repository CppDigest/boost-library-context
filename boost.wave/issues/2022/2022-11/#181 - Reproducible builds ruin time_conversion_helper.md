# #181 - Reproducible builds ruin time_conversion_helper [Open]

> Username: realazthat  
> Created at: 2022-11-17 18:07:31 UTC  
> Updated at: 2023-10-27 18:18:01 UTC  
> Url: https://github.com/boostorg/wave/issues/181  

I use bazel as my build system, and bazel specializes in reproducible builds, which basically means it tries to build everything in a sandbox with limited access to system libraries, and replaces the various date macros with some string like __REDACTED__ . This breaks the time_conversion_helper  and asserts(false) at runtime.  
  
not sure what the best solution is, but it would be great if there was a macro that made the time-based macros optional.  
  
My solution is to fork boost and comment out the assertion (https://github.com/boostorg/wave/blob/99ccc6e3cbd3fa347dc23f10518aa7d33d31573e/include/boost/wave/util/time_conversion_helper.hpp#L127).

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-11-17 20:03:56 UTC  
> Url: https://github.com/boostorg/wave/issues/181#issuecomment-1319138435  

It looks like the purpose of this code is to help produce version strings for Wave itself (including its samples). I think it might be fine to produce `UNKNOWN` as a string in cases where parsing fails, instead of doing this assert. We'd need some test cases too to ensure valid date/time strings didn't start getting misparsed.

---

## Comment 2

> Username: jefftrull  
> Created at: 2023-10-27 18:18:01 UTC  
> Url: https://github.com/boostorg/wave/issues/181#issuecomment-1783323056  

Another idea: I read about [SOURCE_DATE_EPOCH](https://reproducible-builds.org/specs/source-date-epoch/); if we offered a setting that would help you implement that (i.e. by overriding system time) would that suffice?
