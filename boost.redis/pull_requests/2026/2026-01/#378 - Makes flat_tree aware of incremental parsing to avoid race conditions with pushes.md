# #378 Makes flat_tree aware of incremental parsing to avoid race conditions with pushes [Merged]

> Username: anarthal  
> Created at: 2026-01-06 13:15:17 UTC  
> Updated at: 2026-01-07 08:55:27 UTC  
> Merged at: 2026-01-07 08:55:23 UTC  
> Closed at: 2026-01-07 08:55:23 UTC  
> Url: https://github.com/boostorg/redis/pull/378  

Adds the concept of a "temporary working area" to flat_tree. Nodes in this area belong to a partially parsed message, and are hidden from the user. Now flat_tree can be used as the receive response without explicitly handling partial messages.  
Changes `flat_tree::get_view` return type from `const vector&` to `span`.  
Adds `flat_tree::capacity`.  
Splits `generic_flat_response` tests to a separate file and adds extra cases.  
  
close #369

---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-06 13:15:51 UTC  
> Url: https://github.com/boostorg/redis/pull/378#issuecomment-3714660030  

This doesn't change `flat_tree` to a container (will do as a separate PR) but addresses the race condition issue.

---

## Comment 2

> Username: anarthal  
> Created_at: 2026-01-07 08:55:07 UTC  
> Url: https://github.com/boostorg/redis/pull/378#issuecomment-3717892185  

Merging per Marcelo's request.

---
