# #75 Uniform usage of ratio as boost::ratio [Open]

> Username: traversaro  
> Created at: 2024-02-23 11:53:22 UTC  
> Updated at: 2024-02-23 11:53:44 UTC  
> Url: https://github.com/boostorg/chrono/pull/75  

While debugging a strange ABI issue in a downstream Boost Chrono package (https://github.com/RoboStack/ros-noetic/issues/448), I found an inconsistencies between `std::ratio` and `boost::ratio`. I was not able to reproduce the issue, however I noticed that in Boost Chrono there are some inconsistency in the use of `ratio`, that sometimes is used as `boost::ratio`, and sometimes is used just `ratio` (this is probably a leftover from the time where `std::ratio` did not exist).   
  
Regardless of the root issue I was experiencing, I think (but I may be wrong) that it may be beneficial to uniform the usage in the library to always use `boost::ratio`, for consistency and avoid confusion with `std::ratio` .

---
