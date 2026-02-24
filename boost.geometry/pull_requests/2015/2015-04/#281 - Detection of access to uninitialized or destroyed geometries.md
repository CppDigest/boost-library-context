# #281 Detection of access to uninitialized or destroyed geometries. [Merged]

> Username: awulkiew  
> Created at: 2015-04-21 19:32:57 UTC  
> Updated at: 2015-04-22 20:00:27 UTC  
> Merged at: 2015-04-22 13:30:31 UTC  
> Closed at: 2015-04-22 13:30:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/281  

If `BOOST_GEOMETRY_ENABLE_ACCESS_DEBUGGING` is defined getters and setters checks if the data being read was properly initialized and if the object was properly created and wasn't destroyed.  
  
With this change it's easy to detect e.g. the use of references to destroyed temporaries or the use of uninitialized objects.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-04-22 07:45:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/281#issuecomment-95064085  

Good idea. Can you rename m_magic to, for example, m_assigned ? (or, longer but clearer, m_access_debugging_assigned)?  
For the rest I'm OK with merging this.

---
