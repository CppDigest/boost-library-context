# #1427 Annotate facade header with IWYU export annotation [Open]

> Username: hzeller  
> Created at: 2025-08-30 14:54:43 UTC  
> Updated at: 2025-12-10 07:39:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427  

Without that annotation, tools such as `clang-tidy` or the `clangd` language server (as well as many other tools) will complain about headers not directly providing a symbol if users include `geometry.hpp`; With this annotation, they know.  
  
Documentation IWYU  
https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md#iwyu-pragma-begin_exportsend_exports  
  
Documentation llvm include cleaner/clang-tidy/clangd https://clangd.llvm.org/design/include-cleaner#iwyu-pragmas

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-08-31 14:36:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3240189656  

hi Henner, thanks for your PR.  
  
Given the fact that it is a `clang` pragma, widely used by many users nowadays, we can think about something compiler specific indeed.  
  
Do you annotate all, or more, Boost Libraries?

---

## Comment 2

> Username: hzeller  
> Created_at: 2025-08-31 15:11:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3240213407  

Not really a super clang-specific pragma.  
These first showed up in the IWYU tool set (more than a decade ago), then later got also adopted by the clang include cleaner and `clang-tidy`.  
  
Anyay, yes I have open pull request for a few so far. Next to geometry, also for [asio](https://github.com/boostorg/asio/pull/455), [beast](https://github.com/boostorg/beast/pull/3027), and [polygon](https://github.com/boostorg/polygon/pull/98).

---

## Comment 3

> Username: tinko92  
> Created_at: 2025-09-01 14:11:10 UTC  
> Updated_at: 2025-09-01 14:18:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3242529550  

I have no strong opinion on this, so I approve. Since in the commit itself there is not much to review, some general observations:  
  
- Grepping through my entire boost with all libraries checked out at current develop finds no existing IWYU pragma comments (just mentions in .clang-format), so BG would be the first to do this.  
```sh  
grep "IWYU" boost/libs -n -r  
boost/libs/parser/.clang-format:46:CommentPragmas:  '^ IWYU pragma:'  
boost/libs/locale/.clang-format:60:CommentPragmas:  '^ IWYU pragma:'  
boost/libs/mysql/.clang-format:88:CommentPragmas: '(^ IWYU pragma:)|(^\\copydoc )|(^ ?\\n)'  
boost/libs/nowide/.clang-format:53:CommentPragmas:  '^ IWYU pragma:'  
boost/libs/histogram/.clang-format:39:CommentPragmas:  '^ IWYU pragma:'  
boost/libs/redis/.clang-format:84:CommentPragmas: '(^ IWYU pragma:)|(^\\copydoc )|(^ ?\\n)'  
boost/libs/yap/.clang-format:46:CommentPragmas:  '^ IWYU pragma:'  
```  
-  Including e.g. boost/geometry/geometries/geometries.hpp (which is suggested in the BG documentation page for the models) would arguably less objectionable from an IWYU-perspective than including boost/geometry.hpp AFAIU but it also declares nothing and would presumably need the same pragmas. Should this be on more headers then?

---

## Comment 4

> Username: hzeller  
> Created_at: 2025-09-01 18:59:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3243106669  

adding a pragma to `boost/geometry.hpp` won't help much as it in itself includes another facade header, and IWYU will only follow one level deeper. So in my project, i'll include `boost/geometry/geometry.hpp` and am thus only one level removed (and this is why I only added the annotation there)

---

## Comment 5

> Username: tinko92  
> Created_at: 2025-09-02 13:02:41 UTC  
> Updated_at: 2025-09-02 14:25:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3245244689  

Thank you for clarifying.  
  
If I test with the tool, e.g.  
  
```cpp  
#if defined(LEVEL0)  
#include <boost/geometry.hpp>  
#elif defined(LEVEL1)  
#include <boost/geometry/geometry.hpp>  
#elif defined(LEVEL2)  
#include <boost/geometry/geometries/geometries.hpp>  
#else  
#include <boost/geometry/geometries/box.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#endif  
  
using point = boost::geometry::model::d2::point_xy<double>;  
using box = boost::geometry::model::box<point>;  
  
int main() {  
    box a;  
    return 0;  
}  
```  
  
That gets me (where ./geometry is your branch)  
```sh  
include-what-you-use main.cpp -I./geometry/include -I../boost/ -DLEVEL0 # case 1: complains  
include-what-you-use main.cpp -I./geometry/include -I../boost/ -DLEVEL1 # case 2: your include, still complains, on your branch  
include-what-you-use main.cpp -I./geometry/include -I../boost/ -DLEVEL2 # case 3: deeper include, still complains  
include-what-you-use main.cpp -I./geometry/include -I../boost/ # case 4: no complaint (actually including what is used)  
```  
  
So this PR, will suppress a warning from a tool that seems arguably valid (including all of boost geometry is usually including more than what is used, there are specific headers for using specific interfaces) but only if boost geometry is included in way different from what is documented and what most people are probably doing (<boost/geometry/geometry.hpp> instead of <boost/geometry.hpp>) and then not in all cases, only if at least one class or method is used that is declared in a header directly included by boost/geometry.hpp. Is this correct and could any of this be improved?  
  
Would we also want this pragma on e.g. boost/geometry/geometries/geometries.hpp and similar headers like these (in particular the facade headers that are recommended for use by the current boost documentation)? If I add the pragma to the geometries.hpp header, it silences the warning for case 3, still not for case 2 though.

---

## Comment 6

> Username: hzeller  
> Created_at: 2025-09-02 14:31:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3245608828  

It looks like there is no perfect solution as IWYU only follows the exports pragmas one level deep (I am not using the include-what-you-use but the `clangd` language server and `clang-tidy` that also have these implemented). If you use the clangd language server and include the header, it will complain in the editor about headers not defining the symbol directly included.  
  
Maybe the private pragma can help ? https://github.com/include-what-you-use/include-what-you-use/blob/master/docs/IWYUPragmas.md#iwyu-pragma-private ... i have not explored that

---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2025-09-02 15:52:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1427#pullrequestreview-3177188957  

📁 include/boost/geometry/geometry.hpp

```diff
  20 | #ifndef BOOST_GEOMETRY_GEOMETRY_HPP
  21 | #define BOOST_GEOMETRY_GEOMETRY_HPP
```

> Username: barendgehrels  
> Created_at: 2025-09-02 15:52:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#discussion_r2316514791  

I think it's good to add a comment (one or two sentences) giving the background,  
like you did in the PR (maybe more concise). Otherwise this will raise questions after a year or two (by possibly new developers).

> Username: barendgehrels  
> Created_at: 2025-09-16 14:22:32 UTC  
> Updated_at: 2025-09-16 14:22:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#discussion_r2352688931  

@hzeller will you add that comment?


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-10-09 17:59:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3386962257  

The other PR's are still open. Waiting with merging...

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2025-12-08 15:44:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1427#pullrequestreview-3552714784  

I am not against, so I approve. Though it would be good if we wait the other libraries' feedback as well. In any case this goes for 1.91 or later.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2025-12-10 07:39:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1427#issuecomment-3635785805  

See also this comment in one of the linked PR's  
  
>@hzeller, I'm using clangd-20 and clang-tidy-20 with the -misc-include-cleaner check, but I wasn't able to reproduce the warning you mentioned. Could you please provide more details on how to reproduce the issue?  
  
I think this is a random selection of Boost libraries that a particular user needed at that moment. If there are no further requests, and it is not used in any other Boost library, I don't think we should honour this.

---
