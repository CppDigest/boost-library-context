# #590 - variant=release runtime-debugging=on does not enable assertions as stated in documentation [Open]

> Username: Kojoley  
> Created at: 2020-05-01 19:42:35 UTC  
> Updated at: 2021-05-29 17:22:45 UTC  
> Url: https://github.com/boostorg/build/issues/590  

The `runtime-debugging` says:  
> Specifies whether produced object files, executables, and libraries should  
include behavior useful only for debugging, **such as asserts**. Typically, the  
value of this feature is implicitly set by the `variant` feature, but it can be  
explicitly specified by the user. **The most common usage is to build release  
variant with debugging output**.  
  
https://github.com/boostorg/build/blob/7d9866256d1ce46abaf8355bb618cdebab9cfe62/src/tools/features/runtime-feature.jam#L13-L17  
  
And I believed it because the `variant=release` documentation does not list `<define>NDEBUG`  
https://github.com/boostorg/build/blob/7d9866256d1ce46abaf8355bb618cdebab9cfe62/src/tools/features/variant-feature.jam#L24-L27  
while in reality it expands to  
https://github.com/boostorg/build/blob/7d9866256d1ce46abaf8355bb618cdebab9cfe62/src/tools/builtin.jam#L50-L51

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:16 UTC  
> Url: https://github.com/boostorg/build/issues/590#issuecomment-850868306  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
