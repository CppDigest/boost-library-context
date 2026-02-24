# #112 - Audit the intra-doc links to find breakage [Closed]

> Username: tzlaine  
> Created at: 2024-02-23 01:38:01 UTC  
> Updated at: 2024-03-15 21:29:26 UTC  
> Closed at: 2024-03-15 21:29:26 UTC  
> Url: https://github.com/boostorg/parser/issues/112  

There were a bunch of broken links reported in the Boost review.  They were all `_foo()` functions (e.g. `_val()`).  Each of these ends up in its own Doxygen-generated file, like `parser/doc/html/boost/parser/_val.html`.  Problem is, the Github Pages publishing software won't process those files, because of the leading underscore.  So this should have no impact on the final docs on the Boost website.  I claim victory.  Also, while auditing all the links, I did find a real broken one, to the "Expectation points" section.  So that still needs fixing.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-24 03:05:36 UTC  
> Url: https://github.com/boostorg/parser/issues/112#issuecomment-1962229898  

Ok, so apparently you can add an empty file called `.nojekyll` to the root of the `gh-pages` branch, and it fixes the leading-underscore issue.  So, I did that too.
