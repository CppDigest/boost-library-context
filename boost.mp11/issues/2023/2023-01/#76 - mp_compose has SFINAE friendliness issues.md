# #76 - mp_compose has SFINAE friendliness issues [Closed]

> Username: jodyhagins  
> Created at: 2023-01-16 21:55:34 UTC  
> Updated at: 2023-03-26 14:54:38 UTC  
> Closed at: 2023-03-24 12:04:26 UTC  
> Url: https://github.com/boostorg/mp11/issues/76  

The summary is that `mp_compose` does not allow utilization in a detection context.  
  
In the example code, we will use a simple composition that takes one metafunction.  If we use `mp_quote<take3>`, the code compiles and works as expected.  However, if we use `mp_compose<take3>`, the code does not compile - failing to detect a SFINAE violation, and instead generating a hard error.  
  
  
Consider the following code...  
  
```c++  
// A simple metafunction that takes the first three items from a list  
template <typename T>  
using take3 = mp_take<T, mp_int<3>>;  
  
// Directly call take3  
static_assert(mp_same<  
    take3<mp_iota_c<9>>,  
    mp_list<mp_size_t<0>, mp_size_t<1>, mp_size_t<2>>>::value);  
  
// Invoke a quoted take3 (mp_quote<take3>)  
static_assert(mp_same<  
    mp_invoke_q<mp_quote<take3>, mp_iota_c<9>>,  
    mp_list<mp_size_t<0>, mp_size_t<1>, mp_size_t<2>>>::value);  
  
// Invoke a composed take3 (mp_compose<take3>)  
static_assert(mp_same<  
    mp_invoke_q<mp_compose<take3>, mp_iota_c<9>>,  
    mp_list<mp_size_t<0>, mp_size_t<1>, mp_size_t<2>>>::value);  
  
// If we apply the following iteration, we expect to get all triplets of the list.  
using expected = mp_list<  
    mp_list<mp_size_t<0>, mp_size_t<1>, mp_size_t<2>>,  
    mp_list<mp_size_t<3>, mp_size_t<4>, mp_size_t<5>>,  
    mp_list<mp_size_t<6>, mp_size_t<7>, mp_size_t<8>>>;  
  
// First using mp_quote - this works as expected  
static_assert(  
    mp_same<  
        mp_iterate_q<  
            mp_iota_c<9>,  
            mp_quote<take3>,  
            mp_bind_back<mp_drop, mp_int<3>>>,  
        expected>::value);  
  
// Next, simply replace mp_quote with mp_compose.  We expect to get the same answer,  
// but we get a hard compiler error instead.  
static_assert(  
    mp_same<  
        mp_iterate_q<  
            mp_iota_c<9>,  
            mp_compose<take3>,  
            mp_bind_back<mp_drop, mp_int<3>>>,  
        expected>::value);  
```

---

## Comment 1

> Username: jodyhagins  
> Created at: 2023-03-24 18:23:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/76#issuecomment-1483235094  

I had written my own patch, but didn't even submit it as a PR because it was so gross - not near as simple as yours.  
  
That patch is BEAUTIFUL!!!!!  
  
Thanks!

---

## Comment 2

> Username: pdimov  
> Created at: 2023-03-25 01:07:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/76#issuecomment-1483650634  

Looking forward to your Mp11 talk. :-)

---

## Comment 3

> Username: jodyhagins  
> Created at: 2023-03-26 13:31:29 UTC  
> Url: https://github.com/boostorg/mp11/issues/76#issuecomment-1484097576  

Uh oh.  That just raised the stakes.  
  
I'll **gladly** yield my slot if you want it.  
  
I don't consider myself an expert; more like one beggar showing others where they can find food.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-03-26 13:52:01 UTC  
> Url: https://github.com/boostorg/mp11/issues/76#issuecomment-1484103126  

> I'll **gladly** yield my slot if you want it.  
  
That wasn't how I meant that, at all.  
  
I'm glad you find the library useful and will invest the time to make it more popular by doing a talk about it. Thanks.

---

## Comment 5

> Username: jodyhagins  
> Created at: 2023-03-26 14:54:38 UTC  
> Url: https://github.com/boostorg/mp11/issues/76#issuecomment-1484121063  

Sorry - I was trying to be funny and at the same time acknowledge that it would be like me talking about _The Art of Computer Programming_ in front of Donald Knuth.
