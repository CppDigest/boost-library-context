# #403 Handle degenerated segments near poles in spherical and geographic intersection strategies. [Merged]

> Username: awulkiew  
> Created at: 2017-07-02 17:15:10 UTC  
> Updated at: 2017-09-06 12:24:13 UTC  
> Merged at: 2017-07-05 11:51:32 UTC  
> Closed at: 2017-07-05 11:51:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/403  

This PR fixes the ticket: https://svn.boost.org/trac10/ticket/13035.  
  
The problem is that near poles segments may be very short even though the longitudes of the endpoints are different. When the `equals(pt1, pt2)` is used to check if the segment is degenedated it may return false if longitudes are different but still near the pole the distance between the endpoints is close or equal to 0.  
  
The spherical and geographic intersection strategies are modified in a way that distances between the segments are also taken into account when checking if a segment is degenerated to a point.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-07-05 02:38:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/403#issuecomment-312982825  

I'd like to include this fix in 1.65.

---
