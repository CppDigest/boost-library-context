# #176 - fix more warnings? [Closed]

> Username: HDembinski  
> Created at: 2019-03-21 16:59:32 UTC  
> Updated at: 2019-04-21 12:31:47 UTC  
> Closed at: 2019-04-21 12:31:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/176  

Henry suggests to make histogram compile cleanly with `-Wconversion -Wsign-conversion` enabled. The only Boost libs which do that are unordered, container_hash, and gil. Out of these, only gil is a "high-level" library like histogram.  
  
Other warnings to possibly enable:  
`-pedantic -Wstrict-aliasing -fstrict-aliasing -Wextra -Wsign-promo -Wunused-parameter -Wconversion -Wfloat-equal -Wshadow -Wcast-align`

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-04-21 12:31:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/176#issuecomment-485247939  

The following warnings flags will be supported:  
`-pedantic -Wextra -Wsign-compare -Wstrict-aliasing -fstrict-aliasing`  
`-Wconversion and -Wsign-conversion` litter the numerical code with countless casts that obfuscate the code. I tried this and it added nothing useful.
