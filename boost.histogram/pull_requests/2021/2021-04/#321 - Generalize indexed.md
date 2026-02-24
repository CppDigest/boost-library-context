# #321 Generalize indexed [Merged]

> Username: HDembinski  
> Created at: 2021-04-24 15:30:22 UTC  
> Updated at: 2021-04-24 20:37:43 UTC  
> Merged at: 2021-04-24 20:37:43 UTC  
> Closed at: 2021-04-24 20:37:43 UTC  
> Url: https://github.com/boostorg/histogram/pull/321  

Closes #277   
  
```c++  
auto ax = axis::integer<>(0, 4);  
auto ay = axis::integer<>(0, 3);  
  
auto h = make(Tag(), ax, ay);  
for (auto&& x : indexed(h, coverage::all)) x = x.index(0) + x.index(1);  
  
int range[2][2] = {{1, 3}, {0, 2}};  
auto ir = indexed(h, range);  
auto sum = std::accumulate(ir.begin(), ir.end(), 0); // returns 1 + 2 + 2 + 3  
```

---
