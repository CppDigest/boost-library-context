# #29 fix suppressing I/O error in parse_config_file [Open]

> Username: JojOatXGME  
> Created at: 2017-07-16 12:37:25 UTC  
> Updated at: 2020-07-19 01:15:58 UTC  
> Url: https://github.com/boostorg/program_options/pull/29  

corresponding issue: https://svn.boost.org/trac10/ticket/13125

---

## Comment 1

> Username: vprus  
> Created_at: 2017-07-24 19:25:45 UTC  
> Url: https://github.com/boostorg/program_options/pull/29#issuecomment-317528544  

Thanks for the patch, Johannes. I wonder whether it will be even better to do the check inside the   
```  
template<class charT>  
    basic_parsed_options<charT>  
    parse_config_file(std::basic_istream<charT>& is,   
                      const options_description& desc,  
                      bool allow_unregistered)  
```  
function - that way, the error reporting will be done in all cases, not just when the config file is passed via name.

---

## Comment 2

> Username: JojOatXGME  
> Created_at: 2017-07-24 21:59:45 UTC  
> Url: https://github.com/boostorg/program_options/pull/29#issuecomment-317567403  

Since there is currently no way to let `parse_config_file(std::basic_istream<charT>& is, ...)` throw `reading_file`, I was not sure whether it is desired behavior to keep the caller responsible for it. Note that the caller might be considered as the *owner* of the stream. Moreover, the filename is unknown within this function. (It might not even be a file.)  
  
However, since exceptions are already used for various other kinds of errors, it might be more consistent to use them here as well. I do not know what the right solution would be.

---

## Comment 3

> Username: JojOatXGME  
> Created_at: 2019-03-03 17:35:26 UTC  
> Updated_at: 2019-03-03 17:40:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/29#issuecomment-469045343  

Since there was no progress about this pull request for a long time, I decided to start another attempt. I think it is fine that the caller of  
  
```cpp  
template<class charT>  
basic_parsed_options<charT>  
parse_config_file(std::basic_istream<charT>& is,   
                  const options_description& desc,  
                  bool allow_unregistered)  
```  
  
must check itself if there was an error on the stream. I extended the documentation of this function to make it clear. Throwing an exception could also make the function incompatible with earlier versions which do not throw such exception.  
  
However, an caller of  
  
```cpp  
template<class charT>  
basic_parsed_options<charT>  
parse_config_file(const char* filename,  
                  const options_description&,  
                  bool allow_unregistered = false);  
```  
  
cannot check the stream itself. I think it is important to fix this bug and to handle errors while reading the stream.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2019-03-04 00:57:32 UTC  
> Updated_at: 2020-01-25 11:42:53 UTC  
> Url: https://github.com/boostorg/program_options/pull/29#issuecomment-469085323  

# [Codecov](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=h1) Report  
> :exclamation: No coverage uploaded for pull request base (`develop@1083bac`). [Click here to learn what that means](https://docs.codecov.io/docs/error-reference#section-missing-base-commit).  
> The diff coverage is `39.65%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/program_options/pull/29/graphs/tree.svg?width=650&token=UMdM0EgHK3&height=150&src=pr)](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##             develop      #29   +/-   ##  
==========================================  
  Coverage           ?   49.92%             
==========================================  
  Files              ?       23             
  Lines              ?     1388             
  Branches           ?      709             
==========================================  
  Hits               ?      693             
  Misses             ?      110             
  Partials           ?      585  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/program\_options/detail/cmdline.hpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL2NtZGxpbmUuaHBw) | `0% <ø> (ø)` | |  
| [include/boost/program\_options/parsers.hpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvcGFyc2Vycy5ocHA=) | `40% <ø> (ø)` | |  
| [...lude/boost/program\_options/options\_description.hpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvb3B0aW9uc19kZXNjcmlwdGlvbi5ocHA=) | `0% <ø> (ø)` | |  
| [include/boost/program\_options/detail/parsers.hpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZGV0YWlsL3BhcnNlcnMuaHBw) | `72.97% <100%> (ø)` | |  
| [src/cmdline.cpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-c3JjL2NtZGxpbmUuY3Bw) | `45.51% <100%> (ø)` | |  
| [src/options\_description.cpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-c3JjL29wdGlvbnNfZGVzY3JpcHRpb24uY3Bw) | `48.64% <31.25%> (ø)` | |  
| [include/boost/program\_options/errors.hpp](https://codecov.io/gh/boostorg/program_options/pull/29/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wcm9ncmFtX29wdGlvbnMvZXJyb3JzLmhwcA==) | `72.82% <66.66%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=footer). Last update [1083bac...348ec67](https://codecov.io/gh/boostorg/program_options/pull/29?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 5

> Username: JojOatXGME  
> Created_at: 2020-07-19 01:09:00 UTC  
> Updated_at: 2020-07-19 01:15:58 UTC  
> Url: https://github.com/boostorg/program_options/pull/29#issuecomment-660567180  

@vprus I noticed that you have already cherry-picked / rebased my changes for the *master* branch. This explains why I could not reproduce the issue anymore. This actually confused me quite some time ago. :smile:  
  
Is there a reason why  
  
* the changes don't end up in the *develop* branch, even after 2 or 3 years, and  
* the pull request was never closed?  
  
Also note that I added a test and made small changes on the documentation later. This changes did not end up in the *master* branch yet. Not sure if you want to integrate them. Otherwiese, we should maybe close the pull request at some point?

---
