# #59 - Avoid using "<=>" [Closed]

> Username: shiqiao-zhang  
> Created at: 2019-04-27 13:00:50 UTC  
> Updated at: 2019-04-30 16:46:45 UTC  
> Closed at: 2019-04-30 16:46:10 UTC  
> Url: https://github.com/boostorg/utility/issues/59  

<https://www.boost.org/doc/libs/1_70_0/libs/utility/operators.htm#rationale> says:  
  
> [...] Moreover, unless your class has really surprising behavior, some of these related operators can be defined in terms of others (e.g. `x >= y <=> !(x < y)`). [...]  
  
Since `<=>` becomes a single token in C++20 (the [three-way comparison operator](https://en.cppreference.com/w/cpp/language/operator_comparison#Three-way_comparison)), I would suggest rephrasing to prevent possible confusion. Like this:  
  
> [...] Moreover, unless your class has really surprising behavior, some of these related operators can be defined in terms of others (e.g. `x >= y` is equivalent to `!(x < y)`). [...]  
  
That's better, isn't it?

---

## Comment 1

> Username: d-frey  
> Created at: 2019-04-30 16:46:45 UTC  
> Url: https://github.com/boostorg/utility/issues/59#issuecomment-488027504  

Indeed, thanks for the suggestion.
