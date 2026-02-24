# #388 Image Processing docs [Merged]

> Username: simmplecoder  
> Created at: 2019-08-31 19:05:02 UTC  
> Updated at: 2020-08-01 20:09:43 UTC  
> Merged at: 2019-09-03 02:43:42 UTC  
> Closed at: 2019-09-03 02:43:42 UTC  
> Url: https://github.com/boostorg/gil/pull/388  

### Description  
  
Expanded documentation for Harris and Hessian detectors.  
  
### References  
  
https://github.com/boostorg/gil/pull/350, https://github.com/boostorg/gil/pull/364  
  
### Tasklist  
- [X] Check if the docs are in the final output  
- [X] Ensure all CI builds pass  
- [X] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2019-08-31 21:18:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/388#pullrequestreview-282320115  

First, thank you very much for kickstarting the prose documentation for the image processing. I hope @miralshah365 will also fill it in with some content.  
  
Since we rely on Sphinx, see https://github.com/boostorg/gil/blob/develop/doc/README.md, all prose docs must be written in reStructuredText, not Markdown.  
  
Second, in order to tell Sphinx to integrate your pages, you need to add to the table of contets in the `doc/index.rst` lines like this  
  
```  
image_processing/basics  
image_processing/<other page without .rst extension>  
```  
  
e.g. somewhere after `design_guide` and before extensions.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2019-09-02 18:00:29 UTC  
> Url: https://github.com/boostorg/gil/pull/388#issuecomment-527215611  

I agree with @mloskot : markdown documentation such as the toplevel *.md files is intended to be browsed by contributors who review the repo on github (which provides markdown rendering built into their portal) or quickly glance over them in purely textual form on your local machine.  
Any end-user documentation (tutorials, API docs, etc.) needs a bit more care, i.e. is actually compiled into html and possibly other formats using [sphinx.](http://www.sphinx-doc.org) This is published online in https://boostorg.github.io/gil.  
Build the docs locally by running `bjam` / `b2` inside `gil/doc` and review the generated HTML to make sure your changes are compiled correctly.

---

## Review 3 [Commented]

> Username: mloskot  
> Created_at: 2019-09-02 18:11:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/388#pullrequestreview-282683489  

📁 doc/image_processing/basics.rst

```diff
   1 |+ Basics
   2 |+ ------
```

> Username: mloskot  
> Created_at: 2019-09-02 18:10:58 UTC  
> Updated_at: 2019-09-02 18:16:14 UTC  
> Url: https://github.com/boostorg/gil/pull/388#discussion_r320029032  

Any reason you don't follow GIL's markup for headings?  
  
GIL (should) follows markup for sections recommended by [Python guide for documenting](https://devguide.python.org/documenting/#sections) and equivalent [Sphinx reStructuredText Primer](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#sections), that is:  
  
```  
=, for sections  
-, for subsections  
^, for subsubsections  
", for paragraphs  
```  
  
However, in future, this may and we may decide to distinguish chapters and parts as well.is intentional.


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2019-09-02 18:12:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/388#pullrequestreview-282683728  

This looks good to me.  
  
I just had a general comment in https://github.com/boostorg/gil/pull/388#pullrequestreview-282683489

---
