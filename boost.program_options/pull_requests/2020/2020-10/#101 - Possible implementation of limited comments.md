# #101 Possible implementation of limited comments [Open]

> Username: xloem  
> Created at: 2020-10-23 22:20:16 UTC  
> Updated at: 2020-11-15 18:43:02 UTC  
> Url: https://github.com/boostorg/program_options/pull/101  

Provides for `#` characters in configuration file values.  See #100

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-10-24 08:12:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-715878718  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=h1) Report  
> Merging [#101](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/program_options/commit/0414abe3f4cf3bb617b80f2e3fb655b0fd8c1ddb?el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `25.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/101/graphs/tree.svg?width=650&height=150&src=pr&token=UMdM0EgHK3)](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #101   +/-   ##  
========================================  
  Coverage    49.89%   49.89%             
========================================  
  Files           23       23             
  Lines         1385     1387    +2       
  Branches       707      709    +2       
========================================  
+ Hits           691      692    +1       
  Misses         111      111             
- Partials       583      584    +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...clude/boost/program\_options/detail/config\_file.hpp](https://codecov.io/gh/boostorg/program_options/pull/101/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL2NvbmZpZ19maWxlLmhwcA==) | `38.88% <0.00%> (ø)` | |  
| [include/boost/program\_options/parsers.hpp](https://codecov.io/gh/boostorg/program_options/pull/101/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvcGFyc2Vycy5ocHA=) | `40.00% <ø> (ø)` | |  
| [src/parsers.cpp](https://codecov.io/gh/boostorg/program_options/pull/101/diff?src=pr&el=tree#diff-c3JjL3BhcnNlcnMuY3Bw) | `45.61% <0.00%> (ø)` | |  
| [src/config\_file.cpp](https://codecov.io/gh/boostorg/program_options/pull/101/diff?src=pr&el=tree#diff-c3JjL2NvbmZpZ19maWxlLmNwcA==) | `38.02% <33.33%> (+0.34%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=footer). Last update [0414abe...e47c6bd](https://codecov.io/gh/boostorg/program_options/pull/101?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: xloem  
> Created_at: 2020-11-15 16:03:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-727593432  

I got an email mentioning that it might be possible to use `std::quoted` to resolve this, too.  I see the comment is deleted so I infer that they discerned it might not be the right choice, but I'm mentioning it here because I was unaware of `std::quoted`.  
  
If somebody else wants to try adding some more options to this for maintainers to choose from in case they have concerns, using escape characters is another option.  
  
There might have also been a concern around the interface for enabling or disabling it.

---

## Comment 3

> Username: FloopCZ  
> Created_at: 2020-11-15 17:07:37 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-727602876  

Hi, yes, sorry about that, that was my fault. As I have written previously, I believe the proposed technique calls for error-prone config files as I am not aware of any interpreter that behaves like this. If you also consider escape character instead (e.g. similarly to `\` in Bash), `std::quoted` will probably not be suited for that case (as I wrongly believed before). However, you can use a regular expression to parse the line, e.g., `([^=]+)=((?:\\.|[^\\#])*)#{0,1}(.*)` with whitespace trimming should work by a quick test:  
  
```py  
#!/usr/bin/env python3  
import re  
cases = [  
    (r"key=abcd",                    (r"key", r"abcd", r"")),  
    (r" key = abcd # com",           (r" key ", r" abcd ", r" com")),  
    (r" key =abcd#comment",          (r" key ", r"abcd", r"comment")),  
    (r"key =abcd #comment",          (r"key ", r"abcd ", r"comment")),  
    (r" key=abcd\#nocomment",        (r" key", r"abcd\#nocomment", r"")),  
    (r" key = abcd\#nocomment#com",  (r" key ", r" abcd\#nocomment", r"com")),  
    (r" key = abcd\#nocomment###",   (r" key ", r" abcd\#nocomment", r"##")),  
    (r" key = abcd\\#comment",       (r" key ", r" abcd\\", r"comment")),  
    (r" key = abcd\\\#nocomment",    (r" key ", r" abcd\\\#nocomment", r"")),  
    (r" key = abcd\nocomment",       (r" key ", r" abcd\nocomment", r"")),  
]  
rex=r"([^=]+)=((?:\\.|[^\\#])*)#{0,1}(.*)"  
for s, v in cases:  
    assert re.fullmatch(rex, s).groups() == v  
print("OK")  
```  
That could also be the default and only behavior so you would not need to pass an extra argument.  
Explanation of the regex:  
```  
([^=]+)=            key and `=` character  
(?:                 value, i.e., either  
    \\.                any escaped character (including the escape character itself)  
    [^\\#]             or a normal non-escaped non-comment character  
)  
#{0,1}(.*)          comment

---

## Comment 4

> Username: xloem  
> Created_at: 2020-11-15 17:51:06 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-727609222  

Thanks so much for your feedback.  
  
I'm worried adding it as the default behavior could break existing programs when distributions and developers upgrade.  There are a lot of users of programs linked to boost out there, a huge config file space.  
  
Do you think there'd be a solution that preserves the behavior for people who happen to have the edge case of `'a=b\#comment` in a pre-existing config file?  
  
Developers wouldn't usually be able to predict if their users were affected by this change, to know whether to require an earlier version or not when upgrading, and it's a small change that would be hard to get everyone to spread preparation for.

---

## Comment 5

> Username: FloopCZ  
> Created_at: 2020-11-15 18:29:00 UTC  
> Updated_at: 2020-11-15 18:29:16 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-727614560  

You have a good point. However, in my opinion, the edge case `a=b\#comment` (i.e., "escaping" a comment sign while still treating the comment as a comment) is against widely known practices and therefore it will be so very rare that this breaking change will probably not affect anyone.  
  
If it bothers you anyway, the next version of `program_arguments` could also print a warning that the behaviour regarding this edge case has changed (or will change in the future) if encountered, as some other programs do (e.g., `git`, `gcc`, etc.), but I would not bother.  
  
I guess @vprus will have to share his opinion with us. :slightly_smiling_face:

---

## Comment 6

> Username: xloem  
> Created_at: 2020-11-15 18:43:02 UTC  
> Url: https://github.com/boostorg/program_options/pull/101#issuecomment-727616568  

I like the warning solution, although I'm thinking of GUI applications where the config files could even be autogenerated.    
  
I won't likely be working further on this PR for quite some time, so it's mostly just for when the work hours are available from someone.  
  
It seems to me a separate options class, or a separate member function to set the parsing options, might be a more robust solution.

---
