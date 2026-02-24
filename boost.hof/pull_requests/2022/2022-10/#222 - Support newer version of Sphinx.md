# #222 Support newer version of Sphinx [Merged]

> Username: sdarwin  
> Created at: 2022-10-04 23:27:05 UTC  
> Updated at: 2023-01-10 22:11:39 UTC  
> Merged at: 2023-01-10 22:11:39 UTC  
> Closed at: 2023-01-10 22:11:39 UTC  
> Url: https://github.com/boostorg/hof/pull/222  

Hi,  
  
The current boostorg/boost bundle has been created using Sphinx version 1.5.6   
  
If upgrading _all_ packages and Sphinx changes to 5.2.1 , the hof docs are slightly broken.   
  
- No code blocks  
- bullet lists are widely spaced     
  
Researched the problems in https://github.com/sphinx-doc/sphinx/issues .  Ultimately the best solution seems to be switching to the recommended package https://github.com/executablebooks/MyST-Parser because "recommonmark is now deprecated."   The way forward is myst-parser.    The update in this pull request it is detecting if the sphinx version is still 1.5.6, in which case the old methodology continues to be used, or if a newer sphinx, then switch to myst-parser.     
  
The code blocks which were missing, reappear.  So that is a good result.  However, what's interesting is that some of those blocks are highlighted correctly and others are plain without color highlighting.  What is the cause of that?  It seems in the source files that some blocks are marked with backticks and the language "cpp".   Those are rendered.   And in the cases without backticks and language, there is no color.    Even if this is not ideal, at least there is a work-around available: go and  mark those code blocks with backticks, if they should have color highlighting.  
  
In terms of "bullet lists are widely spaced" I will send another pull request to the sphinx-boost repository.

---

## Comment 1

> Username: pfultz2  
> Created_at: 2022-10-05 01:15:42 UTC  
> Url: https://github.com/boostorg/hof/pull/222#issuecomment-1267792144  

The way I understood it before is that when setting `highlight_language = 'cpp'` it should highlight code blocks without a language as C++. Maybe thats not the case anymore with newer Sphinx.   
  
Instead of using fence blocks directly in the header, we could just convert it on the fly in the `extract_doc` function(the [`convert_to_code_fences`](https://github.com/pfultz2/dotfiles/blob/master/bin/slides#L23) function almost does what we want). The code snippets are extracted by cmake and built in CI, and is already looking for the tabbed code blocks.  
  
Also, should we consider updating the doc/requirements.txt with the newer sphinx version?

---

## Review 2 [Commented]

> Username: pfultz2  
> Created_at: 2022-10-05 01:17:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hof/pull/222#pullrequestreview-1130774165  

📁 doc/conf.py

```diff
 415 |-     app.add_transform(AutoStructify)
 416 |-     app.connect('source-read', extract_doc)
 431 |+ if sphinxversion=="1":
```

> Username: pfultz2  
> Created_at: 2022-10-05 01:17:30 UTC  
> Url: https://github.com/boostorg/hof/pull/222#discussion_r987449666  

Can the `if` be moved inside of the `setup` function?


---

## Comment 3

> Username: sdarwin  
> Created_at: 2022-10-05 15:00:12 UTC  
> Url: https://github.com/boostorg/hof/pull/222#issuecomment-1268562598  

- moved the `if` statement inside the `setup` function.  
- update `doc/requirements.txt`. If you publish a new official pip package, requirements could link to it.  
  
I believe that `highlight_language = 'cpp' `should highlight code blocks without a language as C++.   But there is another question of what myst-parser considers to be a code block.   It might require a certain indication, such as backticks or other markers.    
  
As far as "convert it on the fly in the extract_doc function", that sounds good. Maybe you could add it, either in another pull requests, or even here?  The setting "Allow edits and access to secrets by maintainers" is enabled.  Keeping in mind that for the time being we should maintain compatibility with the old build environment also (sphinx 1.5.6).

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-01-10 19:36:17 UTC  
> Url: https://github.com/boostorg/hof/pull/222#issuecomment-1377753428  

Regarding the two open pull requests for docs, I tested all the changes, and they worked for Sphinx 1.5.6 and 5.2.1. An easy way to verify is to merge them both into the develop branch. New docs will appears at https://www.boost.org/doc/libs/develop/libs/hof/doc/html/doc/index.html.   If there are any problems it could be rolled back and not propagated to the master branch.  
  
Only a small number of boost libraries have any issues, such as this one. Most do not use Sphinx.  After this update, it will open the door to upgrade the entire set of packages for all boost docs.

---
