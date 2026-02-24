# #48 - sequence_container_interface's pop_back/pop_front do not account for move only types [Closed]

> Username: Ryan-rsm-McKenzie  
> Created at: 2021-02-01 07:34:47 UTC  
> Updated at: 2021-06-05 22:36:47 UTC  
> Closed at: 2021-06-05 22:36:47 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/48  

`pop_*` is implemented conditionally on the presence of `emplace_*`, invocable with a const l-value reference of `value_type`. However, containers which are templated on move only types cannot `emplace_*` by copy. Normally, this is not a problem, as the check is performed in an unevaluated context, but containers which constrain their `emplace_*` method using sfinae/requires clauses will find this check fails, even though it shouldn't.  
  
[Godbolt](https://godbolt.org/z/1h3MfP)
