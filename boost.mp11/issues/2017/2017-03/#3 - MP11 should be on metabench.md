# #3 - MP11 should be on metabench [Closed]

> Username: odinthenerd  
> Created at: 2017-03-17 16:59:17 UTC  
> Updated at: 2017-05-18 15:05:34 UTC  
> Closed at: 2017-05-18 15:05:34 UTC  
> Url: https://github.com/boostorg/mp11/issues/3  

I for one would like to see how mp11 stacks up in terms of compile time performance.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-03-17 17:40:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287422761  

Any advice on what I need to do to get mp11 there? Neither Cmake nor Ruby are among my strong suits.

---

## Comment 2

> Username: odinthenerd  
> Created at: 2017-03-17 19:07:36 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287445219  

I' embarrassingly bad at both too but @brunocodutra was quite helpful to me getting brigand and kvasir on there so now I understand it enough to get myself into trouble. I'll see what I can do.

---

## Comment 3

> Username: odinthenerd  
> Created at: 2017-03-17 19:17:04 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287447296  

can you list the mp11 equivalent of each of the following algorithms (so I don't get anything wrong):  
all  
any  
at  
count_if  
erase  
filter  
find_if  
fold_left  
fold_right  
insert  
partition  
replace_if  
reverse  
sort  
transform  
  
its ok if you don't have implementations for some we can just leave them out.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-03-17 19:30:08 UTC  
> Updated at: 2017-03-17 19:32:10 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287450042  

```  
#include <boost/mp11.hpp>  
using namespace boost::mp11;  
```  
  
and then  
  
```  
mp_all_of<L, P>  
mp_any_of<L, P>  
mp_at<L, N> // or mp_at_c<L, N>  
mp_count_if<L, P>  
template<class L, class N> using erase = mp_append<mp_take<L, N>, mp_drop_c<L, N::value+1>>;  
mp_copy_if<L, P>  
mp_find_if<L, P>  
mp_fold<L, V, F>  
mp_reverse_fold<L, V, F>  
template<class L, class N, class T> using insert = mp_append<mp_push_back<mp_take<L, N>, T>, mp_drop<L, N>>;  
mp_partition<L, P>  
mp_replace_if<L, P, W>  
mp_reverse<L>  
mp_sort<L, P>  
mp_transform<F, L>  
```  
  
The list type is `mp_list<T...>`.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-03-17 19:31:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287450354  

Incidentally, the benchmarks for `find_if` don't look correct, they look for `n` with the intent to find the last element, but the list contains `n*k+1..n*k+n`, so they don't.

---

## Comment 6

> Username: brunocodutra  
> Created at: 2017-03-17 20:31:16 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-287463380  

Indeed, thanks for pointing that out

---

## Comment 7

> Username: odinthenerd  
> Created at: 2017-03-21 09:32:19 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-288023047  

I think I'm too stupid ;) I just can't seem to get the dependantcy to work. I'll add all the .erb and make a branch where someone else can get it working

---

## Comment 8

> Username: odinthenerd  
> Created at: 2017-03-21 09:33:02 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-288023198  

@pdimov would like to talk a bit about your lib, can you provide an email or follow me on twitter do I can DM you?

---

## Comment 9

> Username: pdimov  
> Created at: 2017-03-21 14:43:51 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-288100495  

Sure, pdimov at pdimov com.

---

## Comment 10

> Username: brunocodutra  
> Created at: 2017-03-21 19:34:43 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-288193509  

@odinthenerd I'll take it from there

---

## Comment 11

> Username: brunocodutra  
> Created at: 2017-03-21 20:14:41 UTC  
> Url: https://github.com/boostorg/mp11/issues/3#issuecomment-288204210  

see ldionne/metabench#174
