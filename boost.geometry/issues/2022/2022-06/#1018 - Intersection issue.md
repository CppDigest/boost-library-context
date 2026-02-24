# #1018 - Intersection issue [Closed]

> Username: xakod  
> Created at: 2022-06-17 15:11:42 UTC  
> Updated at: 2022-06-20 09:16:26 UTC  
> Closed at: 2022-06-20 09:08:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1018  

I have an image contains isolines and I wanna clipping my image by specific clipping polygon.   
I have 2 different clipping polygons, this is an important point.  
For example, my image looks like this:  
![isolines](https://user-images.githubusercontent.com/26579016/174322481-ca3fee79-8d08-42eb-b33f-30700fa351a0.png)  
Desired result looks like this (clipping polygon 2)  
![isolines_clipped_ok](https://user-images.githubusercontent.com/26579016/174322755-d5cbaa9f-8562-4e75-a2fa-4b1bd3136c53.png)  
Clipping polygon can be overlap my image, can be fully inside image.  
Isolines and clipping polygon 1 on one image  
![isolines_with_cliping_area](https://user-images.githubusercontent.com/26579016/174323696-f7c5b3c3-feea-4686-9929-3d55367d8b78.png)  
What if I clip isolines by this clipping area? I looks a bit strange:  
![isolines_clipped_strange](https://user-images.githubusercontent.com/26579016/174324634-229b57d5-44b4-4538-b89e-6323a9f36e32.png)  
Everything is ok but not the bottom left. Ok, go ahead.  
Now we clip isolines by polygon 2:  
![isolines_clipped_wrong](https://user-images.githubusercontent.com/26579016/174325188-7a1cc88f-c5a9-4197-81e7-1db28beaf359.png)  
It looks very strange...  
Example is available here https://github.com/Jihadist/intersection_issue  
My test data is very big so I couldn't create gist. Maybe I do smth wrong. Does someone can help?  
Thank you in advance!  
  
I use Boost/1.79

---

## Comment 1

> Username: xakod  
> Created at: 2022-06-20 09:08:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1018#issuecomment-1160180228  

Sorry, I forgot to correct input geometries

---

## Comment 2

> Username: awulkiew  
> Created at: 2022-06-20 09:15:45 UTC  
> Updated at: 2022-06-20 09:16:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1018#issuecomment-1160188802  

Ok, thanks for the update.
