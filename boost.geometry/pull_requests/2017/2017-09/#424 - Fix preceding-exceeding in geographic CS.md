# #424 Fix preceding/exceeding in geographic CS [Merged]

> Username: awulkiew  
> Created at: 2017-09-17 19:20:10 UTC  
> Updated at: 2017-11-23 04:45:51 UTC  
> Merged at: 2017-10-25 21:28:15 UTC  
> Closed at: 2017-10-25 21:28:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/424  

This is a fix for `get_turns`/`union` in geographic and spherical CS:  
  
    p1 = POLYGON((16 15,-132 10,-56 89,67 5,16 15))  
    p2 = POLYGON((101 49,12 40,-164 10,117 0,101 49))  
  
The above case looks similar to this cartesian case:  
  
![hbcfnhhomlkaeokn](https://user-images.githubusercontent.com/1226951/30524118-c43b5a92-9bed-11e7-80fa-3a5efefaa550.png)  
p1 - red, p2 - green, north pole - blue  
  
In this case for the second polygon very big section box is created and preceding/exceeding may return wrong result for such box. The result is only one intersection point detected in `get_turns`.

---
