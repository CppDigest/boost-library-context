# #338 - Doc build script issues [Closed]

> Username: sdarwin  
> Created at: 2026-01-05 17:33:15 UTC  
> Updated at: 2026-01-08 14:02:37 UTC  
> Closed at: 2026-01-08 14:02:37 UTC  
> Url: https://github.com/boostorg/unordered/issues/338  

> Deletes subdirectory node_modules after Antora generation: this directory has slipped previously in release archives  
  
1. Is this working?  I just downloaded a snapshot of boost 1.91.0, the unordered/doc/node_modules folder exists.  
  
2. There are two similar files doc/antora_docs.sh and doc/build_antora.sh.  At minimum they should be kept in sync, they are going out of sync.  But preferably delete  one of them? (Keep build_antora.sh so it matches `openmethod` and `url`).    
  
Edit:  
  
Or, if both scripts ought to stay, add a comment (in both scripts) explaining the situation

---

## Comment 1

> Username: joaquintides  
> Created at: 2026-01-08 08:18:50 UTC  
> Updated at: 2026-01-08 08:19:40 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3722732977  

Deletion of `node_modules` is implemented in the Jamfile, not in `build_antora.sh`. Which one does the release build script call?  
  
As per the existence of two scripts, yes, I can remove `antora_docs.sh`.

---

## Comment 2

> Username: sdarwin  
> Created at: 2026-01-08 10:14:53 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3723166945  

"Which one"... It really looks like the Jamfile.    
Checking doc previews and comparing those to the superproject build, it seems previews run "cleanup-node-modules" while the full build does not.    
  
On CircleCI, the superproject runs `b2` with /boostrelease since it's a release.    
  
In the Jamfile:  
  
```  
alias boostrelease : html_ ;  
explicit boostrelease ;  
```  
  
  
`html_` leads to   
  
`make html_ : build_antora.sh : @run-script ;  
`  
  
Is it possible `cleanup-node-modules` is not part of `alias boostrelease` ?

---

## Comment 3

> Username: joaquintides  
> Created at: 2026-01-08 11:52:32 UTC  
> Updated at: 2026-01-08 11:54:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3723526381  

Can you check #339? I've tested locally and `node_modules` is now removed when issuing `b2 boostrelease`.

---

## Comment 4

> Username: sdarwin  
> Created at: 2026-01-08 12:15:15 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3723603364  

> Can you check 339  
  
I am not 100% certain, but it looks ok; merge and see if CircleCI succeeds.

---

## Comment 5

> Username: joaquintides  
> Created at: 2026-01-08 12:17:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3723612739  

Reopened in wait for your confirmation.

---

## Comment 6

> Username: sdarwin  
> Created at: 2026-01-08 14:02:37 UTC  
> Url: https://github.com/boostorg/unordered/issues/338#issuecomment-3724018855  

Super. It seems to be fixed.
