# #806 - A tiny grammer mistake in Akima spline example. [Closed]

> Username: Sanhei  
> Created at: 2022-07-20 09:03:40 UTC  
> Updated at: 2022-07-20 15:34:10 UTC  
> Closed at: 2022-07-20 15:34:10 UTC  
> Url: https://github.com/boostorg/math/issues/806  

This is not a bug, if you need C++ to do spline rather than python, but want to solve the problem quickly. In the example, you need to declare template, which miss <vector(double)>. And need to be noticed, this will destroy your vector, actually also make sense, since the y points can be exactly got from spline function, so you don't need them anymore. But in case you miss something.  
  
auto spline = makima<vector(double)>(std::move(x), std::move(y));

---

## Comment 1

> Username: mborland  
> Created at: 2022-07-20 15:33:59 UTC  
> Url: https://github.com/boostorg/math/issues/806#issuecomment-1190436694  

Duplicate of #803
