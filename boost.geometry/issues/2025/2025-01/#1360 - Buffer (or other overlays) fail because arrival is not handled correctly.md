# #1360 - Buffer (or other overlays) fail because arrival is not handled correctly [Open]

> Username: barendgehrels  
> Created at: 2025-01-05 20:01:49 UTC  
> Updated at: 2025-01-05 20:11:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1360  

For example this input:   
`MULTIPOLYGON(((2 8,2 9,3 8,2 8)),((4 6,4 7,5 6,4 6)),((4 2,5 2,4 1,4 2)))`  
  
(as in `recursive_polygons_buffer.cpp`, with distance `1.0` and `miter`)  
  
Delivers a buffer which is incorrect.   
  
![Image](https://github.com/user-attachments/assets/a6d848c6-79fe-4578-9de4-62d99b491d5e)  
  
After some debugging it is caused by ( in `get_turn_info`):  
  
```  
            if (dir_info.arrival[i] == 1)  
            {  
                // The segment arrives at the intersection point, its fraction should be 1  
                // (due to precision it might be nearly so, but not completely, in rare cases)  
                ti.operations[i].fraction = {1, 1};  
            }  
            else if (dir_info.arrival[i] == -1)  
            {  
                // The segment leaves from the intersection point, likewise its fraction should be 0  
                ti.operations[i].fraction = {0, 1};  
            }  
  
```  
  
But if segments are nearly collinear, and touch at the end, the arrival info can apparently not be trusted. This should go or should be changed.  
  
Because if `arrival` is `1`, the fraction is `{1,1}`, meaning: at the end - but the point is not at the end of the segment. That causes wrong sorting along the ring. The current algorithm can sometimes cope with it, sometimes not (in the reported case: not). The sorting should always be correct. And even if the sorting is still correct - the fractions should be correct.  
  
The wrong fractions in the case above:  
```  
ENRICH-assign Ring (s:0, m:0)  
4 cl=-1 meth=i seg=g:0, m:0, p:2, r:-1, s:3 dst=0.707107/2.41421 (0.292893) op=iu (i) to=0 [vx 4]  
0 cl=-1 meth=t seg=g:0, m:0, p:3, r:-1, s:4 dst=1/1 (1) op=ux (u) to=2 [vx 4]  
2 cl=-1 meth=c seg=g:0, m:0, p:3, r:-1, s:4 dst=1/1 (1) op=iu (i) to=1 [vx 5] [not startable]  
1 cl=-1 meth=t seg=g:0, m:0, p:3, r:-1, s:5 dst=1/1 (1) op=ui (u) to=4 [vx 3]  
```   
Turn 0 and 2 above are both on segment 4 and having the same fraction information (`dst`). That would mean they are colocated. This is wrong. Correction gives:  
```  
ENRICH-assign Ring (s:0, m:0)  
4 cl=-1 meth=i seg=g:0, m:0, p:2, r:-1, s:3 dst=0.707107/2.41421 (0.292893) op=iu (i) to=0 [vx 4]  
0 cl=-1 meth=t seg=g:0, m:0, p:3, r:-1, s:4 dst=6.66134e-16/8.88178e-16 (0.75) op=ux (u) to=2 [vx 4]  
2 cl=-1 meth=c seg=g:0, m:0, p:3, r:-1, s:4 dst=1/1 (1) op=iu (i) to=1 [vx 5] [not startable]  
1 cl=-1 meth=t seg=g:0, m:0, p:3, r:-1, s:5 dst=2.41421/2.41421 (1) op=ui (u) to=4 [vx 3]  
```  
  
An example of the wrong sorting:  
```  
5 cl=1 meth=m seg=g:0, m:0, p:6, r:-1, s:6 dst=56.6619/58.6619 (0.965906) op=ux (u) to=4 [vx 6]  
4 cl=-1 meth=i seg=g:0, m:0, p:6, r:-1, s:6 dst=2825.18/2875.12 (0.982632) op=iu (i) to=1 [vx 6]  
1 cl=1 meth=t seg=g:0, m:0, p:6, r:-1, s:6 dst=1/1 (1) op=uu (u) to=4 [vx 9] [not startable]  
```  
The turns (5,1) part of cluster 1 should be sorted consecutively along a ring, it cannot be that there is another turn in between.  
  
Just removing the code above gives correct results, and apparently no regressions. To be checked further.  
  
![Image](https://github.com/user-attachments/assets/f7216fd4-b9d0-4cdd-8535-f225479e7bf7)
