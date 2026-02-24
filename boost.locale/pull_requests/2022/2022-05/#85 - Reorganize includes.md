# #85 Reorganize includes [Merged]

> Username: Flamefire  
> Created at: 2022-05-29 11:48:43 UTC  
> Updated at: 2022-05-30 08:24:15 UTC  
> Merged at: 2022-05-30 08:24:06 UTC  
> Closed at: 2022-05-30 08:24:06 UTC  
> Url: https://github.com/boostorg/locale/pull/85  

- Add missing includes, closes #43, closes #30  
- Remove an unncessary `shared_ptr`, see https://github.com/boostorg/locale/pull/43#issuecomment-545472796  
- Reorganize include order so it is consistent and easier to understand:  
  - public includes (include folder)  include any `<boost/locale/*>`  
  first. At least <boost/locale/config.hpp>  
  - The first include in a cpp file is the corresponding header if any  
  - Then `<boost/locale/*>`, `<boost/*>`, `<*>`, `"*"` in this order  
  - Warning suppression pragmas come last where possible  
  - Use the C++ headers where available (i.e. `<cstring>` not `<string.h>`)  
  
Finally include the examples in the CMake builds to further check that compilation still works.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-29 21:15:35 UTC  
> Url: https://github.com/boostorg/locale/pull/85#issuecomment-1140525845  

# [Codecov](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#85](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f61245a) into [develop](https://codecov.io/gh/boostorg/locale/commit/2e566b8e82626fd2e79c92d2fced7cc8914d1a15?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (2e566b8) will **decrease** coverage by `0.04%`.  
> The diff coverage is `85.71%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #85      +/-   ##  
===========================================  
- Coverage    79.94%   79.90%   -0.05%       
===========================================  
  Files           76       76                
  Lines         5930     5917      -13       
===========================================  
- Hits          4741     4728      -13       
  Misses        1189     1189                
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/conversion.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/date\_time.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lLmhwcA==) | `91.42% <ø> (ø)` | |  
| [include/boost/locale/date\_time\_facet.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lX2ZhY2V0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding\_errors.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfZXJyb3JzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/encoding\_utf.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZW5jb2RpbmdfdXRmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/formatting.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZm9ybWF0dGluZy5ocHA=) | `61.26% <ø> (ø)` | |  
| [include/boost/locale/generator.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJhdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/gnu\_gettext.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ251X2dldHRleHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/hold\_ptr.hpp](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaG9sZF9wdHIuaHBw) | `95.23% <ø> (ø)` | |  
| ... and [44 more](https://codecov.io/gh/boostorg/locale/pull/85/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2e566b8...f61245a](https://codecov.io/gh/boostorg/locale/pull/85?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
