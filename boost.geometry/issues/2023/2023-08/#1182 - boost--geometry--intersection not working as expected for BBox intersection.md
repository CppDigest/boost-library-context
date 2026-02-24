# #1182 - boost::geometry::intersection not working as expected for BBox intersection. [Closed]

> Username: Alphago0506  
> Created at: 2023-08-10 18:30:59 UTC  
> Updated at: 2023-09-05 18:29:09 UTC  
> Closed at: 2023-09-05 18:29:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1182  

I use boost::geometry::intersection to compute the intersection of two box but the result is not as expected.    
  
```  
import numpy as np  
import matplotlib.pyplot as plt  
from matplotlib.patches import Polygon  
  
extended_bbox = np.array([[51221.931803495077, 14310.472435380894], [51223.686991843155, 14314.91852507055], [51216.029134004923, 14317.941627119106], [51214.273945656845, 14313.49553742945]])  
extended_bbox = Polygon(extended_bbox, color='black', fill=False)  
  
query_bbox = np.array([[51217.315441072918, 14312.442738022804], [51218.688255369198, 14317.201686426386], [51216.806967064287, 14317.744381841712], [51215.434152768008, 14312.985433438131]])  
query_bbox = Polygon(query_bbox, color='red', fill=False)  
  
intersection = np.array([[51218.608023028937, 14316.923555896554], [51216.778525905218, 14317.645788748008], [51215.4476422964, 14313.032195749522], [51215.924153023254, 14312.844083018137]])  
intersection = Polygon(intersection, color='blue', fill=False)  
  
fig,ax = plt.subplots()  
  
ax.add_patch(extended_bbox)  
ax.add_patch(query_bbox)  
ax.add_patch(intersection)  
ax.set_xlim([51205,51225])  
ax.set_ylim([14300,14320])  
plt.axis('scaled')  
plt.show()  
```  
result:  
![Screen Shot 2023-08-10 at 14 25 58](https://github.com/boostorg/geometry/assets/50349923/170a467a-098a-4576-b9c5-b76adef2888f)  
  
here the red and black box are input, yet the output is blue polygon and doesn't include the corner point of red box, which seems incorrect from my understanding.  
  
The exact call I use is  
```  
 std::vector<math::geometry::Point2d> intersection;  
 boost::geometry::intersection(extended_target_bbox, query_bbox, intersection);  
```  
  
Is it a bug or do I misunderstand the expected behavior of intersection? Any help is greatly appreciated!

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-08-14 16:12:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1182#issuecomment-1677633739  

Can you please provide a minimal sample code in C++?  
The python code is not useful (for me at least).

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-09-05 18:29:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1182#issuecomment-1707113797  

Closing as no response - if you have still sample code in C++, you can reopen the issue.
