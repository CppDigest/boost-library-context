# #1074 CMA-ES [Merged]

> Username: NAThompson  
> Created at: 2024-01-25 02:10:58 UTC  
> Updated at: 2024-02-09 20:09:57 UTC  
> Merged at: 2024-02-09 20:09:53 UTC  
> Closed at: 2024-02-09 20:09:53 UTC  
> Url: https://github.com/boostorg/math/pull/1074  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-02-08 21:26:58 UTC  
> Url: https://github.com/boostorg/math/pull/1074#issuecomment-1934959767  

@mborland , @jzmaddock : After great and agonizing pain I believe I've got this working.   
  
Let me know if you're interested in this and want time to review, otherwise I'll probably merge in a couple days. . .

---

## Review 2 [Commented]

> Username: mborland  
> Created_at: 2024-02-09 07:54:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1074#pullrequestreview-1871814219  

Without having read the paper here are my trivial comments. The only other thing is there's like zero white space which makes it kind of hard to read. Maybe empty lines before and after outer loops, ifs, equation references, etc would improve readability.

📁 example/cma_es_example.cpp

```diff
   6 |+  */
   7 |+ #if __APPLE__ || __linux__
   8 |+ #include <signal.h>
```

> Username: mborland  
> Created_at: 2024-02-09 07:43:52 UTC  
> Updated_at: 2024-02-09 07:54:45 UTC  
> Url: https://github.com/boostorg/math/pull/1074#discussion_r1483963787  

This should be every POSIX system right? You could `#if __has_include(<unistd.h>)` which is a requirement of every POSIX system.


📁 include/boost/math/optimization/cma_es.hpp

```diff
  76 |+     //auto k = static_cast<size_t>(std::ceil(tmp/params.threads));
  77 |+     //params.population_size = k*params.threads;
  78 |+     params.population_size = static_cast<size_t>(4.0 + floor(3*log(n)));
```

> Username: mborland  
> Created_at: 2024-02-09 07:46:33 UTC  
> Updated_at: 2024-02-09 07:54:45 UTC  
> Url: https://github.com/boostorg/math/pull/1074#discussion_r1483965817  

```diff  
-    params.population_size = static_cast<size_t>(4.0 + floor(3*log(n)));  
+    params.population_size = static_cast<size_t>(4 + floor(3*log(n)));  
```  
  
Some of the `<stdfloat>` types will warn/error on trying to add a double.

---

📁 include/boost/math/optimization/cma_es.hpp

```diff
 190 |+   else {
 191 |+     // See the footnote in Table 1 of the arxiv review:
 192 |+     sigma = 0.3*(params.upper_bounds[0] - params.lower_bounds[0]);
```

> Username: mborland  
> Created_at: 2024-02-09 07:50:40 UTC  
> Updated_at: 2024-02-09 07:54:45 UTC  
> Url: https://github.com/boostorg/math/pull/1074#discussion_r1483969009  

```diff  
-    sigma = 0.3*(params.upper_bounds[0] - params.lower_bounds[0]);  
+    sigma = Real(0.3)*(params.upper_bounds[0] - params.lower_bounds[0]);  
```


---
