# #205 - Copy-assignment from a default-constructed unordered_flat_map can double-free? [Closed]

> Username: hadrielk  
> Created at: 2023-08-12 15:04:23 UTC  
> Updated at: 2023-08-26 14:15:02 UTC  
> Closed at: 2023-08-26 14:15:02 UTC  
> Url: https://github.com/boostorg/unordered/issues/205  

We're trying to use the new `unordered_flat_map`/`node_map` in Boost version `1.83`, with `gcc 7.3`, on x86_64, without SSE2 nor neon - just the plain non-simd group15.  
  
I'm not sure if this is a real bug or something specific to our environment.  
  
I'm hoping someone can try to reproduce it. Unfortunately godbolt only has version `1.82` available, not `1.83`.  
  
---  
  
With the following:  
```cpp  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <string>  
#include <iostream>  
  
int main(int, char**)  
{  
    using Map = boost::unordered_flat_map<size_t, std::string>;  
  
    const Map empty;  
  
    const std::string str = "some long string that is bigger than SSO";  
  
    Map map;  
    for (size_t i = 0; i < 3; ++i) {  
        map.emplace(i, str);  
    }  
  
    map = empty;  
    std::cout << "Will it crash?" << std::endl;  
    map = empty;  
    std::cout << "No" << std::endl;  
}  
```  
  
The version `1.82` implementation does _not_ crash.  
The version `1.83` does crash, in that second `map = empty;` line. It doesn't matter if it's a `unordered_flat_map` or `unordered_node_map`, it has the same issue either way.  
  
The stacktrace shows it is trying to destroy entries in that line, even though the previous `map = empty;` should have cleared the map.  
  
This _only_ happens due to the `empty` being an lvalue, and thus invoking `operator=(const&)`. Had we instead used `map = Map{};` to clear it, it would have invoked `operator=(&&)` and worked fine.  
  
And it _only_ happens when the map is filled with only a few entries - not enough to grow beyond the first group size internally. If it is instead filled with a larger number, it does not crash.  
  
I've gone through `gdb` to try to diagnose it, but it's very hard to figure out why the implementation does some of the things it does in the copy-assignments, and in `match_really_occupied()` and `match_occupied()`.  
  
I'm hoping for some help. 🥺

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-08-12 20:50:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/205#issuecomment-1676094934  

I can look into this first thing on Monday morning. Weekends are busy for me.

---

## Comment 2

> Username: hadrielk  
> Created at: 2023-08-13 02:10:32 UTC  
> Url: https://github.com/boostorg/unordered/issues/205#issuecomment-1676188535  

Thanks @cmazakas. I'll post details of what I could figure out with `gdb`, in case it helps you save time.  
  
Also, fyi the crash happens with SSE2 `group15` as well, fwiw.

---

## Comment 3

> Username: hadrielk  
> Created at: 2023-08-13 02:41:19 UTC  
> Updated at: 2023-08-13 02:46:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/205#issuecomment-1676196145  

OK, I'll try to document what I saw while trying to triage this, in the hope that it helps...  
  
Here's what happens in the crashing case:  
  
1. When the map is initially filled with one or a few entries, the `table_core`'s `arrays` member (the `table_array`) allocates both the `groups` and the `elements` on the heap for the entries, as expected. Its `groups_size_mask` is still just `1`, just like it is for the default-constructed empty one, which is relevant later.  
  
2. For the default-constructed empty map, it has no allocated `elements`, and its `groups` pointer is set to point to the `static` "dummy" group. The dummy group is _not_ initialized to all-zeroes, however; instead its `group15.m[]` values are initialized with some bits set. In the case of the plain `group15`, the `m[0]` `uint64_t` is set to `0x0000000000004000ull`. (Is that bit set for the sentinel? I'm not clear on what `m[0]` or `m[1]` represent exactly)  
  
3. When the map is copy-assigned from the empty one, inside `table_core::operator=(const table_core&)`, it does the following:  
    1. It invokes `clear()`, which (correctly) destroys the entries in the map and resets `size_ctrl.size` to `0`.  
    2. During `clear()`, the `group15.m[]` values are reset to purely `0`, except the last group (the second group, `groups[1]`, in this case) is set as a sentinel, which means `groups[1].m[0]` has a bit set, while `groups[1].m[1]` is just zero.  
    3. I should note that `clear()` never actually _checks_ if the table is empty (ie, `size_ctrl.size == 0`) when checking whether to destroy stuff, however, which will be important later.  
    4. After `clear()`, it then invokes `noshrink_reserve(0)`, which does nothing in this case.  
    5. Then it invokes `copy_elements_from()`, and since in this scenario the `groups_size_mask` of both the map and the empty one are the same, it will invoke `fast_copy_elements_from()`. And since the map has a non-null `elements` still, it will invoke `copy_elements_array_from()` which will do nothing, and then it will also invoke `copy_groups_array_from()`. And `copy_groups_array_from()` will `memcpy` the `groups` from the empty one to this map. Which means the map will now have the same `groups` values of the dummy, since that's what the empty one used for its `groups`. I'm not sure that is intentional? Because it now means `groups[0].m[0]` will have the bit set too, not just `groups[1].m[0]`.  
    6. After all that it's done, so it never de-allocates the buffer pointed to by `elements`, which appears to be intentional. So it's left still pointing to the allocated buffer, which will be relevant later.  
  
4. When the map is copy-assigned to the empty one again (or really it could be copy-assigned to a non-empty one here too and still crash), it does the following:  
    1. Like in (3) above, it invokes `clear()`, but within `clear()` it doesn't check if `size_ctrl.size == 0` first, and instead iterates through each group for `match_really_occupied()`. (it does this because the `elements` pointer is not null)  
    2. Unfortunately `match_really_occupied()` will _not_ return `0`, and instead returns the value of `match_occupied()`. Which for the case of non-SSE plain `group15`, will be the integer `16384`.  
    3. Since it got `16384`, `clear()` thinks there are entries to destroy, and thus tries to destroy already-free'd memory, and boom goes the dynamite.  
  
The reason `match_really_occupied()` returned the value of `match_occupied()` instead of `0` is that the code for it is this:  
```cpp  
  static inline int match_really_occupied(group_type* pg,group_type* last)  
  {  
    /* excluding the sentinel */  
    return pg->match_occupied()&~(int(pg==last-1)<<(N-1));  
  }  
```  
  
The comment _says_ it excludes the sentinel, but that will only happen if `pg==last-1`. If `pg` is _NOT_ equal to `last-1`, then that `int(pg==last-1)` will be `0`, and thus `int(pg==last-1)<<(N-1)` will be `0`, and thus `~()` of it will be all-ones, and thus it won't mask anything from `pg->match_occupied()`.  
  
And in this scenario, `pg` is _NOT_ equal to `last-1`, when `clear()` beings its iteration of the `groups` in the map table. Because in the first iteration `pg` will point to `groups[0]`, while `last-1` points to `groups[1]` (`last` is one past the end here).  
  
So it won't mask the value it extracts from `groups[0].m`, but that value is not `0` - it has a bit set, due to the `memcpy` it did of the dummy group from the empty in the previous copy-assignment, as noted in (3.v) above.  
  
What I'm not clear on is whether it's valid to have the sentinel bit set for `groups[0].m` to begin with. If it is valid, then `match_really_occupied()` is wrong. If it's not valid, then presumably the dummy's `groups[0]` shouldn't have it set.  
  
Also, I think `clear()` should check for `size_ctrl.size == 0` (ie, check `empty()`) before iterating over all the groups. It's a waste of time/perf otherwise, imo.  
  
---  
  
For the non-crashing cases, there are different reasons they don't hit this bug:  
  
1. If a move-assignment is used instead of the copy-assignment, it still invokes `clear()`, but in my particular use-case it will then invoke `reserve(0)`, which will actually end up de-allocating the `elements` buffer altogether. Furthermore it will also `swap()` the table arrays anyway. So the next time a copy-assignment or move-assignment happens, `clear()` won't do anything because `elements` will be null.  
  
    - I should note that this only happens because `if(pocma||al()==x.al())` is `true` for my use-case. If it were `false` instead, then I'm not sure if it would work correctly or not. (it looks like it will, because `copy_elements_from()` is never invoked, but I haven't tested this)  
  
2. If the original map had more elements inserted in it, such that it had more groups, then it won't crash because the `groups_size_mask` values won't be the same for the map and the empty one. So `copy_elements_from()` won't invoke `fast_copy_elements_from()`, which means it won't invoke `copy_groups_array_from()` and won't `memcpy` the dummy groups from the empty one.

---

## Comment 4

> Username: joaquintides  
> Created at: 2023-08-13 16:47:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/205#issuecomment-1676410010  

Hi @hadrielk, this is indeed a bug in `table_core::fast_copy_elements_from`. Please try replacing this line:  
https://github.com/boostorg/unordered/blob/5339bf67d9a445e8f4acb8a3fd963c128e259b80/include/boost/unordered/detail/foa/core.hpp#L1873  
with  
```cpp  
    if(arrays.elements&&x.arrays.elements){  
```

---

## Comment 5

> Username: hadrielk  
> Created at: 2023-08-13 18:05:36 UTC  
> Url: https://github.com/boostorg/unordered/issues/205#issuecomment-1676426474  

@joaquintides yup that fixed it - thanks!
