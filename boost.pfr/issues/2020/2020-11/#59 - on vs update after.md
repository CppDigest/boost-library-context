# #59 - on vs update after... [Closed]

> Username: bilbilemm  
> Created at: 2020-11-14 19:03:46 UTC  
> Updated at: 2020-11-15 15:23:09 UTC  
> Closed at: 2020-11-15 06:10:28 UTC  
> Url: https://github.com/boostorg/pfr/issues/59  

struct test{  
    int a;  
   int b;  
};  
std::is_constructible<test,int,int>::value; // value is false ... on Latest vs 2019   
so pfr can't work

---

## Comment 1

> Username: bilbilemm  
> Created at: 2020-11-14 19:05:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727251705  

Waiting for Microsoft to fix?

---

## Comment 2

> Username: apolukhin  
> Created at: 2020-11-14 19:33:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727254871  

Hm... what error does it produce?  
Mybe there's a workaround for it

---

## Comment 3

> Username: bilbilemm  
> Created at: 2020-11-14 19:40:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727255687  

static_assert(  
        is_aggregate_initializable_n<type, result>::value,  
        "====================> Boost.PFR: Types with user specified constructors (non-aggregate initializable types) are not supported."  
    );  
assert --

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-11-14 20:15:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727259695  

They don't define the `__cpp_lib_is_aggregate` macro? That's strange  
  
Could you please compile and run this file https://github.com/boostorg/pfr/blob/develop/test/print_config.cpp and show the output  
  
If you could you please replace the line  
https://github.com/boostorg/pfr/blob/e15cb59f76df79d808628ffcdbee15779b357a10/include/boost/pfr/detail/fields_count.hpp#L222  
with `#if 1` and show the compile error , that would also help

---

## Comment 5

> Username: bilbilemm  
> Created at: 2020-11-15 06:10:22 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727521497  

solved thanks

---

## Comment 6

> Username: apolukhin  
> Created at: 2020-11-15 15:21:54 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727586835  

What was the problem?

---

## Comment 7

> Username: bilbilemm  
> Created at: 2020-11-15 15:23:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/59#issuecomment-727587039  

The version I use is too old
