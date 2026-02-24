# #172 Feature/rearrange tests [Merged]

> Username: awulkiew  
> Created at: 2014-10-30 00:50:51 UTC  
> Updated at: 2014-10-31 13:39:39 UTC  
> Merged at: 2014-10-31 13:30:03 UTC  
> Closed at: 2014-10-31 13:30:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/172  

The purpose of this PR is to divide tests into smaller groups to e.g. make possible running smaller chunks of tests more conviniently.  
  
For now I moved only distance and set ops. Each algorithm has its own directory but since set operations uses the common code I moved this code to the set_ops directory. There are also some helper *.cpp files there running e.g. all set_ops tests for L/L or P/P combinations. So now it looks like this:  
  
```  
algorithms  
    + set_ops  
    + difference  
    + intersection  
    + ...  
```  
  
So I'm wondering, maybe it'd be better to move the set operations directories deeper e.g. to set_ops directory? And also create a "fake" directories linear_linear and pl_pl and put only Jamfiles in there. Like this:  
  
```  
algorithms  
    +set_ops  
        + difference  
        + intersection  
        + ll  
        + pp  
        + ...  
```  
  
Then it'd be possible to run all of the setops tests, the tests of one algorithm or the tests of all algorithms for L/L or P/P combination. And those helper *cpp files wouldn't be needed, plus it'd be possible to run required tests from bjam level.  
  
Btw, which directory name do you like more "set_ops" or "setops", "rel_ops" or relops?

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-10-30 10:42:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61073077  

I think I would prefer  
  
```  
algorithms  
    +set_ops  
         + difference  
         + intersection  
         + union  
         + sym_difference  
```  
  
I would put code common to all set ops under `algorithms/set_ops`; code for e.g. difference under `algorithms/set_ops/difference`.  
I do not like the idea of have `ll` or `pp` directories. I think it distracts the algorithm-based structure of the unit tests.  
I prefer `set_ops` but I do not have a strong opinion about that.  
  
Finally, I would prefer to keep the .cpp files that correspond to collective tests. They are very convenient for checking everything at once. As you may have noticed I did not include them in the Jamfiles, and that was intentional. They are just helper programs.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-10-30 10:49:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61073793  

What do you think about "full" names set_operations and relational_operations?

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-10-30 10:58:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61074758  

Fine with me.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-10-30 13:26:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61090463  

I moved all of the tests from multi directory.  
  
Should I stop here or move all algorithms to separate directories? After moving files from `multi` there are many of them in the `algorithms` directory. Still probably less than it was before moving some of the tests to separate directories.  
  
I could also group algorithms into categories and move them to categorised directories (similar to relational and set operations), e.g.:  
- `area`, `length`, `perimeter` to `measurement` or `measuring_operations` directory,  
- `num_points`, `num_segments`, etc. to `counting` or `counting_operations`,  
- `append`, `assign`, `convert`, `make` to ...  
- `transform`, `buffer` to ...  
- `simplify`, `correct`, `remove_spikes`, `unique`, to ...  
- etc.  
  
It could also be convenient for the users if we grouped algorithms in the docs. Though I'm ok with leaving the tests and docs as they are now ;)  
  
I detected that the file `multi/algorithms/overlay/multi_overlay_common.hpp` (now in `algorithms/overlay`) isn't included in any test. Should I remove it?

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-10-31 09:23:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61236078  

Thanks for doing this.  
I would say let's leave things as they are now, at least for now.  
I am in favor of merging this PR so that we can continue based on the new structure.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-10-31 13:29:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/172#issuecomment-61259713  

Ok, I'll merge it, additional changes may be done later.

---
