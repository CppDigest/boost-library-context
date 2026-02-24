# #103 - `error_code` does not convert to `result<Eigen::Matrix4d>` [Closed]

> Username: pdimov  
> Created at: 2023-01-20 01:25:52 UTC  
> Updated at: 2023-01-21 22:51:09 UTC  
> Closed at: 2023-01-21 22:51:09 UTC  
> Url: https://github.com/boostorg/system/issues/103  

https://godbolt.org/z/9n9xY33Pn, as reported in https://github.com/boostorg/json/issues/843. `Eigen::Matrix4d` does have a constructor taking any `T`, but it's explicit, and the conversion from `error_code` to `result<Eigen::Matrix4d>` is implicit, so the explicit constructor shouldn't be considered.
