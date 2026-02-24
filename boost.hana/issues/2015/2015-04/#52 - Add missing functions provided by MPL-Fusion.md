# #52 - Add missing functions provided by MPL/Fusion [Closed]

> Username: ldionne  
> Created at: 2015-04-19 18:00:58 UTC  
> Updated at: 2015-08-23 18:41:12 UTC  
> Closed at: 2015-08-23 18:41:12 UTC  
> Url: https://github.com/boostorg/hana/issues/52  

Some functions that might be useful and that are currently missing from Hana are:  
- [x] `erase(sequence, index)`  
- [x] `erase(sequence, from, to)`  
- [x] `insert_range(sequence, index, range)`  
- [x] `erase_key(set-or-map, key)`  
- [x] `unique`, `unique_by`  
  
Another function that is missing from Hana but not in MPL/Fusion is `split_at(index, sequence)`, which should return a pair of two sequences `(before, after)`.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-03 14:12:14 UTC  
> Url: https://github.com/boostorg/hana/issues/52#issuecomment-108443837  

`erase(sequence, index)` is already provided under the name `remove_at`.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-08 22:41:03 UTC  
> Updated at: 2015-06-08 22:42:19 UTC  
> Url: https://github.com/boostorg/hana/issues/52#issuecomment-110164415  

Looks like `erase(sequence, from, to)` won't make it in time for the formal review. I should find a good name that fits well with `remove_at`, and I don't want to rush things.
