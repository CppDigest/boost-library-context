# #142 - Removal of execution_context [Closed]

> Username: theodelrieu  
> Created at: 2020-06-17 10:02:35 UTC  
> Updated at: 2020-06-17 11:48:55 UTC  
> Closed at: 2020-06-17 11:45:47 UTC  
> Url: https://github.com/boostorg/context/issues/142  

Hello,  
  
After upgrading from 1.71.0 to 1.73.0 it seems `execution_context_v2` was removed.  
  
I did not find any explanation nor any migration guide about this, why was it removed? :sweat_smile:

---

## Comment 1

> Username: olk  
> Created at: 2020-06-17 10:11:11 UTC  
> Url: https://github.com/boostorg/context/issues/142#issuecomment-645283849  

It was deprecated in favour of callcc or fiber - I don't want to service multiple APIs in the lib.  
callcc / fiber are more safe to use.  
boost.coroutine2 and boost.fiber are base of fiber from boost.context.

---

## Comment 2

> Username: blastrock  
> Created at: 2020-06-17 10:39:01 UTC  
> Url: https://github.com/boostorg/context/issues/142#issuecomment-645296458  

Hi, I'm a coworker of @theodelrieu .  
  
Removing an API is all fine, but this came as a surprise to us, we didn't notice any deprecation warning. I would have expected a compilation warning, a message in the documentation or something in boost's release note. A compilation warning would definitely have been noticed.  
  
I guess that what is done is done now, but maybe communication can be handled better next time.

---

## Comment 3

> Username: olk  
> Created at: 2020-06-17 11:45:47 UTC  
> Url: https://github.com/boostorg/context/issues/142#issuecomment-645324805  

It was announced see [changelog for 1.72](https://www.boost.org/users/history/version_1_72_0.html).

---

## Comment 4

> Username: olk  
> Created at: 2020-06-17 11:48:55 UTC  
> Url: https://github.com/boostorg/context/issues/142#issuecomment-645326092  

Transforming your code for using boost.context' fiber shouldn't be hard.
