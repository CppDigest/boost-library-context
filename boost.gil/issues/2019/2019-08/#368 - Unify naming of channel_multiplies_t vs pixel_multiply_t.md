# #368 - Unify naming of channel_multiplies_t vs pixel_multiply_t [Closed]

> Username: mloskot  
> Created at: 2019-08-06 22:05:08 UTC  
> Updated at: 2022-04-30 21:53:41 UTC  
> Closed at: 2022-04-30 21:53:41 UTC  
> Url: https://github.com/boostorg/gil/issues/368  

The two are very similar multiplication operations, one for channels and one for pixels, but they are named differently, quite confusingly  
  
https://github.com/boostorg/gil/blob/11c897bb073438b87ca7362f299b278d5c7d236e/include/boost/gil/extension/numeric/channel_numeric_operations.hpp#L75-L76  
  
vs  
  
https://github.com/boostorg/gil/blob/11c897bb073438b87ca7362f299b278d5c7d236e/include/boost/gil/extension/numeric/pixel_numeric_operations.hpp#L79-L82  
  
Ideally, if the naming could be unified.  
  
-----  
  
For example:  
  
1. Rename `channel_multiplies_t` to `channel_multiply_t`  
2. Alias `channel_multiplies_t` to the latter  
3. Mark the `channel_multiplies_t` as deprecated (compile warning)  
  
UPDATE: See the naming discussion below.  
  
-----  
  
@stefanseefeld thoughts?

---

## Comment 1

> Username: marco-langer  
> Created at: 2022-04-29 06:42:43 UTC  
> Url: https://github.com/boostorg/gil/issues/368#issuecomment-1112924453  

The same problem occurs with the divide operations: There is `pixel_divide_t` and `channel_divides_t`.  
  
In the standard we have`std::multiplies` / `std::divides` and _not_ `std::multiply` / `std::divide`.  
  
Which solution is preferred?

---

## Comment 2

> Username: mloskot  
> Created at: 2022-04-29 08:16:10 UTC  
> Updated at: 2022-04-29 08:16:29 UTC  
> Url: https://github.com/boostorg/gil/issues/368#issuecomment-1113010239  

> In the standard we have `std::multiplies` / `std::divides` and not `std::multiply` / `std::divide`.  
  
Good point.  
It looks to me these are named as in "this functor multiplies a and b" and it makes sense.  
But, why there is `std::negate` instead of `std::negates` as "this functor negates"?  
I always thought the C++ standard prefers singular/impersonal forms like in `std::accumulate` and not "this `std::accumulates`", etc.   
That is why I suggested `multiply` instead of `multiplies`, but I am not going to die hard for it ;)  
  
Let's go for the C++ standard naming and unifying this way: `channel_<std equivalent name>_t`

---

## Comment 3

> Username: marco-langer  
> Created at: 2022-04-29 08:35:55 UTC  
> Updated at: 2022-04-29 08:41:50 UTC  
> Url: https://github.com/boostorg/gil/issues/368#issuecomment-1113034112  

Just a guess, but having `std::accumulate` as an algorithm and `std::multiply` as a functor would be quite confusing. People would expect `std::multiply` to be an algorithm as well.   
  
(`std::negate` does not follow this logic though)

---

## Comment 4

> Username: mloskot  
> Created at: 2022-04-29 08:43:52 UTC  
> Url: https://github.com/boostorg/gil/issues/368#issuecomment-1113042293  

>  People would expect `std::multiply` to be an algorithm as well.  
  
Yes, that's reasonable explanation.  
  
Let's have `channel_multiplies_t` then.
