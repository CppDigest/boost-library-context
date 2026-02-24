# #1359 - Buffer fails because a start turn is discarded [Open]

> Username: barendgehrels  
> Created at: 2025-01-05 12:15:04 UTC  
> Updated at: 2025-01-05 12:15:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1359  

With this wkt:  
`MULTIPOLYGON(((2 8,2 9,3 8,2 8)),((7 1,7 2,8 1,7 1)),((5 2,5 3,6 2,5 2)),((2 2,2 3,3 3,2 2)),((1 3,1 4,2 4,1 3)),((6 4,6 5,7 4,6 4)),((9 8,8 8,9 9,9 8)),((9 8,9 7,8 7,9 8)))`  
  
And with miter, buffer distance `1.0` (as in `recursive_polygons_buffer.cpp` where this case comes from)  
  
There is one turn discarded because it is a start turn.  
  
See next picture, turn `0` is discarded because of `1` - but it should not.  
  
<img width="1073" alt="Image" src="https://github.com/user-attachments/assets/dd89d6c8-15ae-4e66-a736-dca619a51fc4" />
