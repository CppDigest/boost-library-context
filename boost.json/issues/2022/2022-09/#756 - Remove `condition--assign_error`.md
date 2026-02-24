# #756 - Remove `condition::assign_error` [Closed]

> Username: grisumbras  
> Created at: 2022-09-26 10:15:10 UTC  
> Updated at: 2022-10-19 20:10:14 UTC  
> Closed at: 2022-10-19 20:10:14 UTC  
> Url: https://github.com/boostorg/json/issues/756  

None of its error codes have anything to do with assignment. They are in fact conversion errors. In #741 a `condition::conversion_error` will be added, so all those error codes should be associated with it instead.
