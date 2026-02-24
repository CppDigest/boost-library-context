# #548 - Documentation of cflags not reflected in common.jam code [Open]

> Username: eldiener  
> Created at: 2020-03-12 07:26:13 UTC  
> Updated at: 2021-05-29 18:22:25 UTC  
> Url: https://github.com/boostorg/build/issues/548  

The documentation for the 'cflags' feature in the latest Boost Build docs says:  
  
"For cflags that is both the C and C++ compilers, for cxxflags that is the C++ compiler"  
  
but this is not reflected in the common.handle-options rule, which has 'cflags' being set for the toolset's 'compile.c' rule rather than for the toolset's 'compile' rule, while the 'compileflags' feature, which is no longer documented, is set for the 'compile' rule.  
  
I believe that the way that the the common.handle-options rule is coded is the way that Boost Build used to document the 'cflags' and 'compileflags' features. Most toolsets do set cflags as a 'compile' option, but a few toolsets still set 'compileflags' as a 'compile' option. At the very least  it seems as if common.handle-options needs to be changed. I can submit a PR for that change, but first I want to run this through here as an issue and get agreement that the the common.handle-options rule should be changed since it affects a great deal of jam code.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:55 UTC  
> Url: https://github.com/boostorg/build/issues/548#issuecomment-850877285  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
