# #69 Add a note to invocation page explaining about trace(enable) [Merged]

> Username: jefftrull  
> Created at: 2020-02-16 23:20:20 UTC  
> Updated at: 2020-02-16 23:56:57 UTC  
> Merged at: 2020-02-16 23:56:52 UTC  
> Closed at: 2020-02-16 23:56:52 UTC  
> Url: https://github.com/boostorg/wave/pull/69  

IMO this will resolve the --traceto usage confusion (at least, it might have resolved mine) and thus #61

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2020-02-16 23:35:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/69#pullrequestreview-359439032  

📁 doc/wave_driver.html

```diff
 171 |     trace log is put into the standard error stream (stderr).</p>
 172 |+   <p dir="ltr">Tracing is only performed for portions of the input where it has been
 173 |+     explicitly enabled via the <tt>#pragma wave trace(enable)</tt> directive.
```

> Username: hkaiser  
> Created_at: 2020-02-16 23:35:17 UTC  
> Updated_at: 2020-02-16 23:55:40 UTC  
> Url: https://github.com/boostorg/wave/pull/69#discussion_r379943845  

Perhaps we should explicitly mention the inline `_Pragma` version as it's more useful for this kind of thing:  
```diff  
-    explicitly enabled via the <tt>#pragma wave trace(enable)</tt> directive.  
+    explicitly enabled via the <tt>#pragma wave trace(enable)</tt> or <tt>_Pragma("wave trace(enable)")</tt> directive.  
```


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2020-02-16 23:35:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/69#pullrequestreview-359439058  

Thanks a lot!

---
