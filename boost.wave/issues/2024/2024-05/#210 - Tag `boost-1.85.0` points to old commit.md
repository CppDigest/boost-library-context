# #210 - Tag `boost-1.85.0` points to old commit [Closed]

> Username: mfep  
> Created at: 2024-05-23 11:54:09 UTC  
> Updated at: 2024-06-14 19:47:07 UTC  
> Closed at: 2024-06-14 19:47:07 UTC  
> Url: https://github.com/boostorg/wave/issues/210  

# Summary  
  
Git tags `boost-1.85.0` and `boost-1.85.0.beta1` point to commit `e02cda69e4d070fd9b16a39282d6b5c717cb3da4`, which is the same as tags `boost-1.84.0` and `boost-1.84.0.beta1`. I assume that this is a mistake, and the 1.85 tags should point to the latest commit on `master`, of hash `c11757decf393f6f1cf46f3f70b0bf892cdda5ee`.  
  
Apparently the `boost_1_85_0.tar.gz` downloadable from boost.org also includes the changes that are merged after the current `boost-1.85.0` tag.  
  
This discrepancy is problematic, since some packagers (e.g. vcpkg) pull Wave based on the tag name, therefore their users get an older version of Wave, even if the packaged Boost version is 1.85.  
  
# Expected  
  
The `boost-1.85.0` git tag points to commit `c11757decf393f6f1cf46f3f70b0bf892cdda5ee`.

---

## Comment 1

> Username: jefftrull  
> Created at: 2024-05-23 18:07:47 UTC  
> Url: https://github.com/boostorg/wave/issues/210#issuecomment-2127759762  

Thanks for letting us know; I am checking to see if I'm allowed to move the tag.

---

## Comment 2

> Username: jefftrull  
> Created at: 2024-05-23 18:18:55 UTC  
> Url: https://github.com/boostorg/wave/issues/210#issuecomment-2127777536  

The release managers are on it (other libraries are also affected)

---

## Comment 3

> Username: jefftrull  
> Created at: 2024-06-14 19:47:07 UTC  
> Url: https://github.com/boostorg/wave/issues/210#issuecomment-2168659427  

This appears to have been resolved, with the 1.85.0 tag now pointing at c11757 as expected
