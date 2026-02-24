# #28 - Missing parallel_stable_sort with comparator and thread count [Closed]

> Username: dstahlke  
> Created at: 2018-10-30 00:46:39 UTC  
> Updated at: 2021-01-24 12:16:05 UTC  
> Closed at: 2021-01-24 12:16:05 UTC  
> Url: https://github.com/boostorg/sort/issues/28  

The detail class contains `parallel_stable_sort(Iter_t first, Iter_t last, Compare comp, uint32_t num_thread)` but the callable wrappers at the end of parallel_stable_sort.h are missing this function.

---

## Comment 1

> Username: fjtapia  
> Created at: 2018-10-30 21:59:32 UTC  
> Url: https://github.com/boostorg/sort/issues/28#issuecomment-434485564  

Give me a few days to solve it  
  
El mar., 30 oct. 2018 a las 1:46, Dan Stahlke (<notifications@github.com>)  
escribió:  
  
> The detail class contains parallel_stable_sort(Iter_t first, Iter_t last,  
> Compare comp, uint32_t num_thread) but the callable wrappers at the end  
> of parallel_stable_sort.h are missing this function.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/28>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ADrumKK87ocDyBlgbZp9EfucHEXa2exWks5up6FwgaJpZM4YA9xb>  
> .  
>

---

## Comment 2

> Username: fjtapia  
> Created at: 2018-11-05 19:25:55 UTC  
> Url: https://github.com/boostorg/sort/issues/28#issuecomment-436003201  

The problem is resolved. You can find the new code in  
https://github.com/boostorg/sort  
  
or If you prefer add the next lines to the end of the  
parallel_stable_sort.hpp  
  
//  
//  
-----------------------------------------------------------------------------  
// function : parallel_stable_sort  
/// @brief : parallel stable sort with 3 parameters.  
///  
/// @param first : iterator to the first element of the range to sort  
/// @param last : iterator after the last element to the range to sort  
/// @param comp : object for to compare two elements pointed by Iter_t  
/// iterators  
/// @param nthread : Number of threads to use in the process. When this  
value  
/// is lower than 2, the sorting is done with 1 thread  
//  
-----------------------------------------------------------------------------  
template<class Iter_t, class Compare>  
void parallel_stable_sort (Iter_t first, Iter_t last, Compare comp,  
uint32_t nthread)  
{  
stable_detail::parallel_stable_sort<Iter_t, Compare>  
(first, last, comp, nthread);  
}  
  
Thnks by your interest. Yours  
  
Francisco Tapia  
  
El mar., 30 oct. 2018 a las 22:59, Francisco José Tapia (<fjtapia@gmail.com>)  
escribió:  
  
> Give me a few days to solve it  
>  
> El mar., 30 oct. 2018 a las 1:46, Dan Stahlke (<notifications@github.com>)  
> escribió:  
>  
>> The detail class contains parallel_stable_sort(Iter_t first, Iter_t  
>> last, Compare comp, uint32_t num_thread) but the callable wrappers at  
>> the end of parallel_stable_sort.h are missing this function.  
>>  
>> —  
>> You are receiving this because you are subscribed to this thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/sort/issues/28>, or mute the thread  
>> <https://github.com/notifications/unsubscribe-auth/ADrumKK87ocDyBlgbZp9EfucHEXa2exWks5up6FwgaJpZM4YA9xb>  
>> .  
>>  
>

---

## Comment 3

> Username: dstahlke  
> Created at: 2018-11-06 18:05:25 UTC  
> Url: https://github.com/boostorg/sort/issues/28#issuecomment-436350176  

Thank you!
