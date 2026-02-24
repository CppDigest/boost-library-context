# #32 depinst option to reject dependency cycles [Merged]

> Username: grisumbras  
> Created at: 2025-10-10 14:42:04 UTC  
> Updated at: 2025-10-10 23:40:32 UTC  
> Merged at: 2025-10-10 16:40:47 UTC  
> Closed at: 2025-10-10 16:40:47 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32  

Hopefully this will work better than the previous approach.  
  
I had to split directories into main and extra, because otherwise tracking what comes from where becomes onerous.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-10-10 14:55:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostdep/pull/32#pullrequestreview-3324098986  

📁 depinst/depinst.py

```diff
  93 | 
  93 |- def scan_directory( d, x, gm, deps ):
  94 |+                     raise DependencyCycle( 'Dependency cycle detacted: ' + '->'.join(dep_path) + '->' + mod )
```

> Username: pdimov  
> Created_at: 2025-10-10 14:55:49 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#discussion_r2420676762  

Typo: detacted -> detected.  
  
Also, ' -> ' please, instead of '->'.


---

## Comment 2

> Username: pdimov  
> Created_at: 2025-10-10 14:57:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#issuecomment-3390620473  

This looks good. But I don't want the stack trace. Catch the exception, print the message, and exit.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-10-10 15:41:23 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#issuecomment-3390837938  

I don't think that `vprint(0` is what we want there, because (a) fatal errors should go to stderr and (b) they shouldn't be suppressed by `-q`.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-10-10 16:42:53 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#issuecomment-3391138517  

Perfect, thank you.  
  
Unfortunately the print to stderr appears before the normal output in GHA's log:  
```  
Error: Dependency cycle detected: geometry -> graph -> geometry  
Installing: config, headers, ../tools/boost_install, ../tools/build, ../tools/cmake, geometry  
Installing: config, range, rational, concept_check, math, core, static_assert, iterator, numeric/conversion, endian, throw_exception, type_traits, predef, variant, graph, assert, algorithm, mpl, lexical_cast, tokenizer, tuple, container, serialization, integer, qvm, function_types, multiprecision, thread, array, variant2, fusion, any, polygon  
Error: Process completed with exit code 1.  
```  
I tried `sys.stdout.flush()`, but it didn't help.  
  
Any ideas how this could be fixed?

---

## Comment 5

> Username: grisumbras  
> Created_at: 2025-10-10 17:37:20 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#issuecomment-3391446571  

The only thing I can suggest is to add `2>&1` to the command.

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-10-10 23:40:32 UTC  
> Url: https://github.com/boostorg/boostdep/pull/32#issuecomment-3392593946  

Yes, this output buffering seems to be a "feature" of Github Actions, there's nothing to be done from our side.  
  
But I'm starting to think that vprint should have actually gone to stderr to begin with, as depinst doesn't print any "program output" per se, only status messages, which (one might argue) are stderr material.

---
