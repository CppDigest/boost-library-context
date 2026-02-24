# #98 - Use clang-format [Closed]

> Username: Flamefire  
> Created at: 2022-07-01 15:17:45 UTC  
> Updated at: 2022-10-11 08:12:22 UTC  
> Closed at: 2022-10-11 08:12:22 UTC  
> Url: https://github.com/boostorg/locale/issues/98  

@artyom-beilis Would you mind if I used clang-format to format the sources and a CI job to enforce this? I'd do the same as in Boost.Nowide.  
  
The changeset will be rather large, but the inconsistencies keep bugging me, like spaces or not around operators, trailing whitespace etc. So I'd rather do a one-time reformat and then have cleaner diffs later on.
