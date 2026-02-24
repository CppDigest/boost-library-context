# #1357 Fix/use biconnected components [Closed]

> Username: barendgehrels  
> Created at: 2024-12-21 16:59:53 UTC  
> Updated at: 2024-12-28 10:26:30 UTC  
> Closed at: 2024-12-28 10:26:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1357  

This is the PR using Boost.Graph biconnected components to detect isolation.  
  
From Boost.Graph:  
  
![image](https://github.com/user-attachments/assets/dd865c76-e58e-4346-bbc0-5b6355bae505)  
  
The numbers are the biconnected components. The letters are the articulation points, but they are not necessary in the end (though for graph display it might be useful).  
  
The big advantages:  
* it is simpler than the isolation checks in `traversal_switch_detector.hpp`  
* it is a standard algorithm  
* **we can use it to follow the registered component_id** (future PR)  
* the last point would make all traversal code much simpler  
  
It fixes the issues also mentioned in PR 1350 and 1356  
  
NOTE: it's only commit 61b002b5929e52746a81e31dbbbc0c76b050a5da  
  
The basic functionality is shown in the next (manually edited) picture (the basis comes from overlay.cpp -> geojson -> qgis)  
  
![image](https://github.com/user-attachments/assets/161acbfc-9c4f-4e68-8a47-37628324e84b)  
  
The corresponding graph-viz-graph is here. It uses the same turn indexes and component ids. Clusters are denoted as negative indexes. Extra nodes (necessary for if a turn is traveling to itself) have high numbers (>10000)  
  
![image](https://github.com/user-attachments/assets/a07a9306-3d46-469d-a4c4-1082fc601fe4)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-12-28 10:26:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1357#issuecomment-2564290405  

As I had in mind, we can use this method for traversal too.  
It is in a promising state now.  
Closing this draft.

---
