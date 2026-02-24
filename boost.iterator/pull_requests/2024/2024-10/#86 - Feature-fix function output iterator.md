# #86 Feature/fix function output iterator [Closed]

> Username: gogagum  
> Created at: 2024-10-10 15:55:41 UTC  
> Updated at: 2024-10-10 18:24:40 UTC  
> Closed at: 2024-10-10 17:50:09 UTC  
> Url: https://github.com/boostorg/iterator/pull/86  

Fixes https://github.com/boostorg/iterator/issues/85#issue-2578996112

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-10-10 16:08:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/86#issuecomment-2405524494  

Is the removal of `output_proxy` necessary? I would prefer to keep it and avoid adding assignment operators to the iterator.

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2024-10-10 16:09:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iterator/pull/86#pullrequestreview-2360833255  

📁 include/boost/iterator/function_output_iterator.hpp

```diff
  33 |     typedef void                value_type;
  65 |-     typedef void                difference_type;
  34 |+     typedef std::ptrdiff_t      difference_type;
```

> Username: Lastique  
> Created_at: 2024-10-10 16:09:33 UTC  
> Url: https://github.com/boostorg/iterator/pull/86#discussion_r1795738187  

This requires `#include <cstddef>`.


---

## Comment 3

> Username: gogagum  
> Created_at: 2024-10-10 17:50:09 UTC  
> Url: https://github.com/boostorg/iterator/pull/86#issuecomment-2405713328  

Well, removal of `output_proxy` is not necessary. Will prepare another pull request.

---

## Comment 4

> Username: gogagum  
> Created_at: 2024-10-10 18:24:38 UTC  
> Url: https://github.com/boostorg/iterator/pull/86#issuecomment-2405772493  

Fix with no `output_proxy` removal: https://github.com/boostorg/iterator/pull/87

---
