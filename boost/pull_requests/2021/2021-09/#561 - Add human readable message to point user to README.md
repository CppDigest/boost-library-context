# #561 Add human readable message to point user to README [Open]

> Username: ykarazia  
> Created at: 2021-09-16 01:53:03 UTC  
> Updated at: 2022-12-23 23:23:33 UTC  
> Url: https://github.com/boostorg/boost/pull/561  

for missing files.  
  
Issue 558 was encountered due to incorrect clone.  
  
resolves #558

---

## Review 1 [Changes requested]

> Username: Flamefire  
> Created_at: 2022-12-16 16:22:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/561#pullrequestreview-1221113423  

This now always requires the $build_script where it did not before. Maybe use a function instead

📁 bootstrap.sh

```diff
 194 | 
 195 | my_dir=$(dirname "$0")
 196 |+ build_script=$my_dir/tools/build/src/engine/build.sh
```

> Username: Flamefire  
> Created_at: 2022-12-16 16:20:56 UTC  
> Updated_at: 2022-12-16 16:22:14 UTC  
> Url: https://github.com/boostorg/boost/pull/561#discussion_r1050930614  

```diff  
-build_script=$my_dir/tools/build/src/engine/build.sh  
+build_script="$my_dir/tools/build/src/engine/build.sh"  
```


---
