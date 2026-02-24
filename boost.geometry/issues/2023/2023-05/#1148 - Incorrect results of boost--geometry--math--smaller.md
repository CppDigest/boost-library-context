# #1148 - Incorrect results of boost::geometry::math::smaller [Open]

> Username: vissarion  
> Created at: 2023-05-19 15:39:30 UTC  
> Updated at: 2023-05-19 15:39:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1148  

There are some failing tests in [circleCI](https://app.circleci.com/pipelines/github/boostorg/geometry/393/workflows/0f08835f-b25a-4112-b290-47348ca4a762/jobs/19548).   
  
The commit that introduced that fail was https://github.com/boostorg/geometry/commit/092ab9da349dd0dab8b610407b0275b192d0477c in particular the replacement of `boost::minmax_element` with `std::minmax_element`.   
  
However, it appears that the issue is related to `boost::geometry::math::smaller` and the inconsistent (and wrong) results it returns in the case of floating points numbers that are very close each other. e.g. (1, 1-eps), (1, 1-eps/2) etc (where `eps=std::numeric_limits<T>::epsilon()`.  
  
I will dig more into it next week.
