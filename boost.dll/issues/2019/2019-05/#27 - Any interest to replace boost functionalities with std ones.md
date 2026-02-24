# #27 - Any interest to replace boost functionalities with std ones ? [Closed]

> Username: Firefly35  
> Created at: 2019-05-29 09:54:29 UTC  
> Updated at: 2020-01-23 07:38:58 UTC  
> Closed at: 2020-01-23 07:38:58 UTC  
> Url: https://github.com/boostorg/dll/issues/27  

Hi Antony,  
do you think there is some value to replace some of boost::dll boost dependencies with std:: ones when available ?  
Typically, I think about shared_ptr, type_traits, move, asserts and so on..

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-06-22 09:10:19 UTC  
> Url: https://github.com/boostorg/dll/issues/27#issuecomment-504647442  

Yes, but without breaking the C++03 builds. And with a macro to change the dependencies back to the Boost ones.

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-01-23 07:38:58 UTC  
> Url: https://github.com/boostorg/dll/issues/27#issuecomment-577552659  

Come back with a PR if there's still interest in such functionality
