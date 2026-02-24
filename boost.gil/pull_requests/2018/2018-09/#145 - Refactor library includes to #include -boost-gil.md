# #145 Refactor library includes to #include <boost/gil/...> [Merged]

> Username: mloskot  
> Created at: 2018-09-28 13:23:36 UTC  
> Updated at: 2020-03-10 20:44:15 UTC  
> Merged at: 2018-09-28 19:19:18 UTC  
> Closed at: 2018-09-28 19:19:18 UTC  
> Url: https://github.com/boostorg/gil/pull/145  

Group include directives, sort within group:  
  
* In headers of GIL core and extensions:  
  
  1. `boost/gil/extension/*`  
  2. `boost/gil/*`  
  3. `boost/*`  
  4. C++ standard library headers  
  
* In programs:  
  
  1. `boost/gil/*`  
  2. `boost/*`  
  3. C++ standard library headers  
  4. `"xxx.hpp"` for local headers  
  
Add basic guidelines to CONTRIBUTING.md.  
Add/Remove `#include <boost/config.hpp>` or std headers un/necessary.  
Rename `gil_concept.hpp` to `concepts.hpp`.  
Remove `gil_all.hpp` - we already have all-in-one `boost/gil.hpp`.  
Tidy up and unify copyright and license header.  
Tidy up formatting and excessive whitespaces in some comments.  
Remove Doxygen block with file description, author, date, etc.  
Remove dead or commented pragmas and directives.  
Trim trailing whitespaces.  
  
-----  
  
This is quite a big changeset, so detailed review may be not quite practical, so I listed all kinds of changes in the log. I might have skipped listing one or two things, like `Add or copy TODO comment where obvious` :-)  
  
### References  
  
* [Do we need doxygen file header?](https://lists.boost.org/boost-gil/2018/09/0084.php)  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-09-28 13:26:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/145#pullrequestreview-159857716  

If the tests run through I do approve this long pull request.

---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-09-28 13:31:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/145#pullrequestreview-159860013  

📁 CONTRIBUTING.md

```diff
 425 |+     ```
 426 |+ * Group `#include` directives and order alphabetically within each group, as follows:
 427 |+     * In headers of GIL core and extensions:
```

> Username: stefanseefeld  
> Created_at: 2018-09-28 13:31:43 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221253035  

I think this is way too much detail. We are not IBM or Microsoft (or any of those big corporations). While I appreciate consistency, I wouldn't want to establish policies dictating the exact ordering of includes.

> Username: mloskot  
> Created_at: 2018-09-28 13:36:11 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221254389  

I did not aim for a big fat corporation formalism really :-)  
Order of includes *from specific to general*, quite helps to ensure each header includes all that is required.  
In fact, this already helped to identify a couple of places where includes where missing, eg.  
* `#include <boost/gil/extension/io/png/detail/supported_types.hpp>` missing from `#include <boost/gil/extension/io/png/detail/reader_backend.hpp>`  
* `#include <csetjmp>` from `#include <boost/gil/extension/io/jpeg/detail/base.hpp>` and `#include <boost/gil/extension/io/jpeg/detail/reader_backend.hpp>`  
  
Anyhow, I can remove those points.

> Username: mloskot  
> Created_at: 2018-09-28 16:09:36 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221305390  

I believe this has been resolved too. Do you see any more changes necessary @stefanseefeld ?

> Username: stefanseefeld  
> Created_at: 2018-09-28 17:08:44 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221321058  

It all looks good to me now. Thanks for working on it !

> Username: mloskot  
> Created_at: 2018-09-28 17:09:38 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221321283  

Right, I was hoping to get your Approve hit :) Thx


---

## Review 3 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-09-28 13:32:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/145#pullrequestreview-159860517  

📁 CONTRIBUTING.md

```diff
 437 |+       4. Other third-party and system headers
 438 |+       5. `#include "xxx.hpp"` for directory local headers
 439 |+ * All public headers should be placed in `boost/gil/` or `boost/gil/<component>/`.
```

> Username: stefanseefeld  
> Created_at: 2018-09-28 13:32:55 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221253410  

This (as well as the next lines) is good !

> Username: mloskot  
> Created_at: 2018-09-28 13:38:02 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221254907  

So, to get it right, you suggest to remove from  
`* Group ...`  
to  
`     5. `#include "xxx.hpp"` for directory local headers`  
inclusive. Correct?

> Username: stefanseefeld  
> Created_at: 2018-09-28 14:01:44 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221263055  

Yes. (I'm not against the idea to order includes. I'm against putting such detail into a coding guide.)  
However, you make a good point: it might be good to add a point:  
* make sure that headers are self-contained, i.e. that they include all headers they need.

> Username: mloskot  
> Created_at: 2018-09-28 14:12:04 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221266408  

To summary:  
* Replace the headers grouping guidelines with the "Make sure..."  
  
Correct?  
  
Sorry to be PITA, but I want to nail the PR update with one shot to save on Travis/AppVeyor time :)

> Username: stefanseefeld  
> Created_at: 2018-09-28 14:14:50 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221267406  

Yes ! :-)

> Username: mloskot  
> Created_at: 2018-09-28 14:16:00 UTC  
> Updated_at: 2018-09-28 14:26:54 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221267788  

OK

> Username: mloskot  
> Created_at: 2018-09-28 14:28:34 UTC  
> Updated_at: 2018-09-28 14:31:07 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221272249  

Done, but with a twist ;)  
  
I've moved the detailed guidelines on the include directives as "here is what can help" Wiki:  
https://github.com/boostorg/gil/wiki/Include-Directives-Order  
  
I think it's quite helpful to have this order listed somewhere. I hope it's OK now.

> Username: stefanseefeld  
> Created_at: 2018-09-28 14:29:28 UTC  
> Updated_at: 2018-09-28 14:29:29 UTC  
> Url: https://github.com/boostorg/gil/pull/145#discussion_r221272560  

Perfect, thanks !


---
