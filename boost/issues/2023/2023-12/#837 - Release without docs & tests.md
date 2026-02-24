# #837 - Release without docs & tests [Open]

> Username: enginelesscc  
> Created at: 2023-12-14 10:29:52 UTC  
> Updated at: 2023-12-14 14:23:53 UTC  
> Url: https://github.com/boostorg/boost/issues/837  

Boost releases are really huge, too huge.  
  
I've tried removing anything not required to build & use boost (just roughly, didn't check for everything):  
- Boost 1.84.0 uncompressed standard release: ~493MiB  
- Boost 1.84.0 roughly stripped: ~180MiB  
  
I'd like to see separate boost releases with these unnecessary amounts of data stripped.  
- no tests  
- no docs (html/xmldoc/else)  
  
> Why?  
  
I find it useful for things like CMake (src containing tar.gz, extracted on configure), portable projects etc etc. Given not everyone wants to use system-installed boost.  
  
Alot of people will never touch these docs/tests/etc, its usually faster to just google for boost docs and use the docs hosted on boost.org/doc.  
  
All these files also add a 'massive' impact to file io on extract/move of boost given the sheer amount of files, even if its just around 500mib.  
  
All things considered I'm sure a less 'bloated' boost would be quite welcomed.

---

## Comment 1

> Username: sdarwin  
> Created at: 2023-12-14 14:23:52 UTC  
> Url: https://github.com/boostorg/boost/issues/837#issuecomment-1855945153  

There is a (recent) work-in-progress pull request in the release-tools repository to build/distribute source-only boost.
