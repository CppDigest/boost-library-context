# #653 - boost::geometry union & difference &r-tree error [Open]

> Username: ChenI-Hsuan  
> Created at: 2019-05-17 05:50:25 UTC  
> Updated at: 2019-12-28 16:52:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/653  

I want to "union" or "clipper" two rectangle but I always get the wrong output.  
so I used  "fsanitize=undefined" command to check my code, and it report a overflow error.  
I think the all points of the tow rectangle in the integer range,so my question is if the points range  
will be "long long" How do I define the type of point_xy to make sure it doesn't overflow when I used the function "union_" "difference" , and some time I used boost r-tree will also get the error.  
![boost1](https://user-images.githubusercontent.com/50729390/57905892-afc1d500-78aa-11e9-86bf-bc84b302d215.PNG)  
![boost2](https://user-images.githubusercontent.com/50729390/57905900-b2242f00-78aa-11e9-9952-10c69886083f.PNG)  
![boost3](https://user-images.githubusercontent.com/50729390/57905902-b5b7b600-78aa-11e9-9d3c-3ecd414d2782.PNG)

---

## Comment 1

> Username: awulkiew  
> Created at: 2019-12-28 16:52:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/653#issuecomment-569432256  

Yes, this is caused by the fact that too small coordinate_type is used. And currently Boost.Geometry does nothing about it. You could try passing a CalculationType of greater size only for the purpose of calculation with the strategy explicitly, e.g.:  
  
    bg::difference(blue, green, output,  
                   bg::strategy::intersection::cartesian_segments<long long>());  
  
I can imagine that by default the library could care about overflow though. So my question is: @barendgehrels do we consider it a bug?  
  
@ChenI-Hsuan Do you have a test case for the R-tree?
