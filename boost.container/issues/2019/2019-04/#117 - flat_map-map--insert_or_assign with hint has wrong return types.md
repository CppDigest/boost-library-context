# #117 - flat_map/map::insert_or_assign with hint has wrong return types [Closed]

> Username: igaztanaga  
> Created at: 2019-04-23 12:15:57 UTC  
> Updated at: 2019-04-23 15:03:27 UTC  
> Closed at: 2019-04-23 15:03:27 UTC  
> Url: https://github.com/boostorg/container/issues/117  

Those overloads return "std::pair<iterator, bool>" instead of just "iterator". It must be fixed and better unit tested.
