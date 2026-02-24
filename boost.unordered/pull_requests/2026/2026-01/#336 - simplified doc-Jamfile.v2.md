# #336 simplified doc/Jamfile.v2 [Merged]

> Username: joaquintides  
> Created at: 2026-01-02 10:54:28 UTC  
> Updated at: 2026-01-04 15:46:05 UTC  
> Merged at: 2026-01-03 15:50:37 UTC  
> Closed at: 2026-01-03 15:50:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/336  

* Removed `install` step as it duplicated generated HTML files and Antora already puts its output in the final destination anyway.  
* Deletes subdirectory `node_modules` after Antora generation: this directory has slipped previously in release archives (seemingly, only `bin` directories are automatically located and removed).  
  
Fixes #330.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-02 11:00:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/336#issuecomment-3705091759  

An automated preview of the documentation is available at [https://336.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html](https://336.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-02 11:00:51 UTC

---

## Comment 2

> Username: sdarwin  
> Created_at: 2026-01-02 12:20:14 UTC  
> Url: https://github.com/boostorg/unordered/pull/336#issuecomment-3705216788  

why not put the docs in `doc/html/` instead of `doc/html/unordered/` ?

---

## Comment 3

> Username: joaquintides  
> Created_at: 2026-01-02 12:44:15 UTC  
> Url: https://github.com/boostorg/unordered/pull/336#issuecomment-3705253251  

> why not put the docs in `doc/html/` instead of `doc/html/unordered/` ?  
  
Backwards compatibility.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2026-01-02 13:46:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/336#issuecomment-3705355403  

> Backwards compatibility  
  
OK.    
  
I think, in the future, if the "redirect to latest version" implements more logic to check the presence or absence of the redirect file, it could also include logic to fix `unordered` as an exception.  However a prerequisite is getting a basic feature working,  which is not there yet.

---

## Review 5 [Commented]

> Username: grisumbras  
> Created_at: 2026-01-04 11:22:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/336#pullrequestreview-3624955667  

📁 doc/Jamfile.v2

```diff
  14 |+ actions cleanup-node-modules
  15 |+ {
  16 |+     bash -c "rm -rf node_modules"
```

> Username: grisumbras  
> Created_at: 2026-01-04 11:21:36 UTC  
> Updated_at: 2026-01-04 11:22:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/336#discussion_r2659592270  

`bash -c` shouldn't be necessary here. It is needed in `run-script` because otherwise building from the doc directory would invoke `build_antora.sh` (and not the relative path to that script), which will fail.  
  
`rm` is a shell command, so it doesn't need another shell wrapper. On the other hand, `node_modules` is incorrect, as it _only_ works if b2 is invoked from the doc directory. Replace with this:  
```  
import path ;  
path-constant DOC_DIR : . ;  
.node_modules = [ path.join $(DOC_DIR) node_modules ] ;  
  
actions cleanup-node-modules  
{  
    rm -rf $(.node_modules)  
}  
```


---
