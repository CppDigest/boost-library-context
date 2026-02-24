# #221 - collection_size_type copy.hpp: The first header filename w/ a space in the name? [Open]

> Username: zaufi  
> Created at: 2020-09-30 20:54:14 UTC  
> Updated at: 2024-04-18 19:10:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/221  

Hi,  
  
I've thought the [filenames w/ spaces](https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/collection_size_type%20copy.hpp) (especially headers) are prohibited in Boost libraries... Anything has changed?  
  
BTW, I don't see any usage of this file in the code...

---

## Comment 1

> Username: psigen  
> Created at: 2021-03-04 05:53:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/221#issuecomment-790315665  

@zaufi it looks like this was simply an accidentally (auto-renamed) copy of the other file that was accidentally commited:  
  
https://github.com/boostorg/serialization/commit/07c99b8649d7bb900271ca93ba3eea9dcb478f42#diff-c7259865ae65dd94c715482e2abf4fbb3ee9f77e63eb1b6677eada21bd239b08  
  
It's been removed from the repo, so I think this is resolved, we just need to wait for the next release cycle.

---

## Comment 2

> Username: vervaekejonathan  
> Created at: 2023-04-24 15:34:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/221#issuecomment-1520400970  

This file is still present in the latest (1.82.0) version downloaded here: https://boostorg.jfrog.io/artifactory/main/release/1.82.0/source/boost_1_82_0.tar.gz

---

## Comment 3

> Username: sfc-gh-ebrossard  
> Created at: 2024-04-18 19:10:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/221#issuecomment-2064998370  

The file has been deleted since 1.84, so this can be closed. https://github.com/boostorg/serialization/tree/boost-1.84.0/include/boost/serialization
