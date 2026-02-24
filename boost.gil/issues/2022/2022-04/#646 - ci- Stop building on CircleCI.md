# #646 - ci: Stop building on CircleCI [Closed]

> Username: mloskot  
> Created at: 2022-04-12 18:56:15 UTC  
> Updated at: 2022-04-12 18:58:47 UTC  
> Closed at: 2022-04-12 18:57:06 UTC  
> Url: https://github.com/boostorg/gil/issues/646  

Apparently, the CircleCI setup for GIL has rotten enough to become unusable:  
  
![image](https://user-images.githubusercontent.com/80741/163033415-32d698cb-d22a-4f80-9c53-9418e576724a.png)  
  
![image](https://user-images.githubusercontent.com/80741/163033466-cf34ca4d-27b3-418d-8c75-c053fddc4687.png)  
  
We are considering complete switch over to GitHub Actions only with minimal builds, see https://github.com/boostorg/gil/issues/642#issuecomment-1092730524, removing GIL from the CircleCI is aligned with that plan and should lower the maintenance overhead.
