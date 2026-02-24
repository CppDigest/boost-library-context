# #173 - The type T is used differently twice in the Callable definition [Closed]

> Username: pfultz2  
> Created at: 2017-09-10 18:58:23 UTC  
> Updated at: 2018-02-14 06:09:56 UTC  
> Closed at: 2018-02-14 06:09:56 UTC  
> Url: https://github.com/boostorg/hof/issues/173  

You use type T twice with different meaning  
  
"The type |T| satisfies |Callable| if"  
  
"if |f| is a pointer to member function of class |T|:"  
  
I believe that here the T are not the same.
