# #150 Restore enabling Large File Support when building directory tools [Closed]

> Username: ala-ableton  
> Created at: 2020-07-02 07:31:58 UTC  
> Updated at: 2020-07-05 19:16:35 UTC  
> Closed at: 2020-07-02 10:42:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/150  

See commit message for more details.  
  
It might make sense to extract the 43 lines that are then duplicated between `src/directory.cpp` and `src/operations.cpp` to a separate file, but I'll let the maintainers decide.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-07-02 10:43:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/150#issuecomment-652931996  

I've made a larger fix for this. Thanks for the report.

---

## Comment 2

> Username: ala-ableton  
> Created_at: 2020-07-05 19:16:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/150#issuecomment-653927694  

@Lastique thanks for the quick fix!

---
