# #235 - remove corpse `nt_query_directory_file_api` symbol [Closed]

> Username: jurko-gospodnetic  
> Created at: 2022-05-09 14:00:27 UTC  
> Updated at: 2022-05-09 15:22:01 UTC  
> Closed at: 2022-05-09 14:21:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/235  

`nt_query_directory_file_api` symbol seems like it's just a corpse  
  
defined and set to point to a dynamically loaded function, but never used anywhere  
  
just noticed it while updating some of our local Boost filesystem library patches to fit the 1.79.0 release

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-09 14:21:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/235#issuecomment-1121168809  

It is used in directory iterator implementation.

---

## Comment 2

> Username: jurko-gospodnetic  
> Created at: 2022-05-09 15:22:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/235#issuecomment-1121243894  

true, I somehow missed the `libs/filesystem/src/directory.cpp` file in my grep results... sorry for the noise
