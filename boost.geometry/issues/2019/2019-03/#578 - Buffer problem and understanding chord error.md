# #578 - Buffer problem and understanding chord error [Open]

> Username: henzim  
> Created at: 2019-03-25 11:37:29 UTC  
> Updated at: 2019-03-25 14:27:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/578  

I have a problem with boost::geometry::buffer. Basically I offset/buffer one linestring by a certain value (e.g. 4) using a certain number of points_per_circle (in my case 180). Now, I offset a second linestring, which lies inside or at most on the first linestring. The second linestring is offset by a value smaller than the first  
4 - chord_error   
and I expected there to be no intersections. However, in (few) cases there are intersection points between the two offsets.  
  
I compute the chord error as   
chord_error(npts, radius) =  ( 1. - cos( Pi / npts)) * radius  
  
Maybe the problem has to do with my interpretation of **points_per_circle** as I expected to be able to derive from it a smallest number that can be used to guarantee that the second offset lies completely within the first.  
  
Please advice.  
  
The following program demonstrates my problem:  
https://wandbox.org/permlink/wvGM3SFlM5MXLx5Z  
  
LineString ls0 (green) lies within or on ls1 (red).   
![image](https://user-images.githubusercontent.com/31598216/54916544-0984cf00-4efa-11e9-9fb3-1d537b52ebe0.png)  
  
From a large box around ls1 is subtract the buffer of ls1, this yields **remains_of_aabb** (brown).  
![image](https://user-images.githubusercontent.com/31598216/54916625-3a650400-4efa-11e9-992f-b9e059162658.png)  
  
The buffer of ls0 (blue) now intersects remains_of_aabb, and lies as much as 89.9959-89.9987 = -0.0028 outside.  
![image](https://user-images.githubusercontent.com/31598216/54916663-59fc2c80-4efa-11e9-8b37-b2bbe9ccb1b5.png)  
  
![image](https://user-images.githubusercontent.com/31598216/54916807-a47da900-4efa-11e9-93e7-a90ee89a2410.png)  
  
Thanks in advance

---

## Comment 1

> Username: henzim  
> Created at: 2019-03-25 14:27:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/578#issuecomment-476220773  

To provide some more information - this is how I imagined **points_per_circle**.  
I assumed that **points_per_circle** are uniformly distributed around "the circle" or proportionally at corners where only an arc of the circle is needed.  
![image](https://user-images.githubusercontent.com/31598216/54927066-c59dc400-4f11-11e9-826f-025752668865.png)  
  
Hence I thought, if I subtract this number from the offset distance, then the second offset (green) and in particular its linearization is guaranteed to lie within the first offset  
![image](https://user-images.githubusercontent.com/31598216/54927460-7c9a3f80-4f12-11e9-9edc-d0f02ad42eb8.png)
