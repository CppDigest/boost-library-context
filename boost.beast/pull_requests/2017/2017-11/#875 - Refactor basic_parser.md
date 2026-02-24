# #875 Refactor basic_parser [Closed]

> Username: djarek  
> Created at: 2017-11-07 19:04:34 UTC  
> Updated at: 2019-09-10 21:02:10 UTC  
> Closed at: 2017-11-09 09:10:34 UTC  
> Url: https://github.com/boostorg/beast/pull/875  

- Add missing noexcept specifiers and const qualifiers.  
- Fix parser conversion constructor not copying header_limit_ and status_ fields.  
- Use default special member functions when possible.  
- Zero-initialize non-class members.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-11-07 19:25:06 UTC  
> Url: https://github.com/boostorg/beast/pull/875#issuecomment-342593568  

The commit message is not "sterile" (i.e. it contains colorful language). Consider this instead:  
```  
Refactor basic_parser:  
  
* Add missing noexcept specifiers and const qualifiers.  
* Fix parser conversion constructor not copying header_limit_ and status_ fields.  
* Use default special member functions when possible.  
* Zero-initialize non-class members.  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2017-11-07 21:28:48 UTC  
> Updated_at: 2017-11-07 22:42:52 UTC  
> Url: https://github.com/boostorg/beast/pull/875#issuecomment-342628229  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=h1) Report  
> Merging [#875](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6eba0e8f9ef81bf38285a7eee260078684611e2b?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/875/graphs/tree.svg?height=150&width=650&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=tree)  
  
```diff  
@@            Coverage Diff             @@  
##           develop    #875      +/-   ##  
==========================================  
- Coverage     95.3%   95.3%   -0.01%       
==========================================  
  Files          104     104                
  Lines        10456   10454       -2       
==========================================  
- Hits          9965    9963       -2       
  Misses         491     491  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/basic\_parser.ipp](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `92.37% <ø> (-0.09%)` | :arrow_down: |  
| [include/boost/beast/http/basic\_parser.hpp](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2Jhc2ljX3BhcnNlci5ocHA=) | `100% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=footer). Last update [6eba0e8...5317773](https://codecov.io/gh/boostorg/beast/pull/875?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-09 09:10:34 UTC  
> Url: https://github.com/boostorg/beast/pull/875#issuecomment-343092197  

Merged to **version 140**, minus the extraneous `noexcept`

---
