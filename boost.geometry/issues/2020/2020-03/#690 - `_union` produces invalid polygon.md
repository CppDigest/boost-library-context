# #690 - `_union` produces invalid polygon [Closed]

> Username: staffik  
> Created at: 2020-03-30 14:59:26 UTC  
> Updated at: 2021-07-28 13:07:34 UTC  
> Closed at: 2021-07-28 13:07:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/690  

Here is a minimal working example: [mwe.zip](https://github.com/boostorg/geometry/files/4403631/mwe.zip)  
  
The attached image presents the geometry that boost thinks is valid (it is a point overlapping with a segment).  
But looking at the image we see a short line that probably should not be visible there, so probably the polygon should be invalid at this stage.  
  
Then we do an union and difference, after that polygon still is valid. But then, after we add a point that is far away from the polygon, it becomes invalid.  
  
Small translations of coordinates, make that there is no error.  
![Screenshot from 2020-03-30 16-21-45](https://user-images.githubusercontent.com/18039094/77927421-7ff3a400-72a7-11ea-9583-ead4cf088397.png)

---

## Comment 1

> Username: tinko92  
> Created at: 2020-04-12 23:38:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/690#issuecomment-612692900  

This is not a solution and I may be missing something but I hope the following helps with diagnosing the issue.  
  
If I disable rescaling (at heast that is what I hope I did, here is the patch: [0001-disable-rescaling.patch.txt](https://github.com/boostorg/geometry/files/4467287/0001-disable-rescaling.patch.txt) ), it seems to work. At least it produces a Multi_Polygon that passes as valid and looks like I think it should look, here is the result I get:  
  
![result](https://user-images.githubusercontent.com/17624459/79081465-f10e6f00-7d1d-11ea-80f6-9b0f6307bd55.png)  
  
To the maintainer who this will be assigned to: I don't know all the reasons for using rescaling in union_ but if it could be avoided if there was a robust version of side_by_intersection for FP coordinate types, that does not rely on rescaling then I could maybe help with that. There is already a robust side_by_triangle in the extensions. Because that may be a feasible (as far as I can tell) but nontrivial effort, I want to ask in advance, whether that would solve the issue and whether that would be considered beneficial to Boost.Geometry.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2020-04-13 11:12:13 UTC  
> Updated at: 2020-04-13 11:13:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/690#issuecomment-612857433  

@tinko92 thanks for your analysis. An easier patch is just to define `BOOST_GEOMETRY_NO_ROBUSTNESS`, which causes all rescaling to be turned off. That setting will be the default in the future.  
  
The function `side_of_intersection` will be gone earlier, after merging my PR #692   
Besides that, it was only used in buffer, and this is not a buffer problem. So there is no need to make it robust.  
  
Verifying if the robust side_by_triangle works well in all cases is not done as far as I know (at least not by me), but I don't think it will solve all the problems, because they are not all side related (though many are).

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-07-28 13:07:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/690#issuecomment-888294139  

Closing because it works well with `BOOST_GEOMETRY_NO_ROBUSTNESS` which will be the default in (hopefully) 1.78
