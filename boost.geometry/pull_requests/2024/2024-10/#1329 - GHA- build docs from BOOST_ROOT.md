# #1329 GHA: build docs from BOOST_ROOT [Open]

> Username: sdarwin  
> Created at: 2024-10-11 16:08:03 UTC  
> Updated at: 2024-10-30 20:37:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1329  

Hi,  
I have some scripts that build each boost library's documentation individually, to test they are working as expected. (Those scripts are in release-tools/build_docs).    
  
For all other boost libraries, this technique works:  
  
```  
cd $BOOST_ROOT  
./b2 _path_to_library_docs_  
```  
  
boostorg/geometry is an exception. This pull request demonstrates that. Would you be able to decipher why it happens and adjust the process?  
  
Notice that even the "Build examples" job follows this pattern.  
  
```  
cd $BOOST_ROOT  
./b2 _path_to_examples_  
```  
  
With earlier versions of geometry, I was implementing a hack, that probably wasn't ideal.   
- Manually run ./b2 $librarypath/doc/src/docutils/tools/doxygen_xml2qbk  
- That built dist/bin/doxygen_xml2qbk which I added to PATH, so it could be discovered.    
  
It solved the problem temporarily.  Now the hack is not working either, but a more official solution is preferable, anyway.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-10-30 18:48:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1329#issuecomment-2448079800  

Thanks @sdarwin  
I didn't look in detail yet, but the CI fails on this PR. This means it's not backwards compatible. How can that be fixed?

---

## Comment 2

> Username: sdarwin  
> Created_at: 2024-10-30 18:55:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1329#issuecomment-2448091180  

@barendgehrels this is both an issue and a pull request.    
  
The geometry docs are not building in the same way that other boost libraries do.  The CI failure here demonstrates that.     
  
`./b2 _path_to_library_docs_`  
  
should work, but it doesn't.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-10-30 20:37:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1329#issuecomment-2448325674  

> @barendgehrels this is both an issue and a pull request.  
  
OK I see. So it proves the CI doesn't work. But it gets a bit a difficult state now. Because it's not an issue (because a PR) and the PR itself is not good.  
  
@vissarion any idea how we can solve this?

---

## Review 4 [Changes requested]

> Username: barendgehrels  
> Created_at: 2024-10-30 20:37:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1329#pullrequestreview-2406259760  

Prevent merge

---
