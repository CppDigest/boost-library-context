# #1325 Feat/enhance tag casts [Merged]

> Username: barendgehrels  
> Created at: 2024-10-07 18:38:07 UTC  
> Updated at: 2024-12-14 11:13:06 UTC  
> Merged at: 2024-10-09 15:34:41 UTC  
> Closed at: 2024-10-09 15:34:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1325  

This PR:  
* uses `tag_cast_t` i/o `tag_cast`  
* where this happens, also uses `tag_t`  
* uses `cs_tag_t` i/o `cs_tag`  
* extracts `single_tag_base_of` to (renamed) a separate meta-function file `primary_single_tag` (I think it's more clear)

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-10-09 11:44:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1325#pullrequestreview-2356900672  

LGTM thanks!

---
