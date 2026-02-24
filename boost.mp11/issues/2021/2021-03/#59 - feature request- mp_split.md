# #59 - feature request: mp_split [Closed]

> Username: u3shit  
> Created at: 2021-03-10 21:01:24 UTC  
> Updated at: 2021-03-16 19:41:33 UTC  
> Closed at: 2021-03-12 17:23:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/59  

I'm trying to port some software from [brigand](https://github.com/edouarda/brigand) (which sadly doesn't look maintained anymore) to boost mp11 and this is the only function I need that doesn't have an equivalent in mp11.  
  
`mp_split<L<T1_1, T1_2, ..., T1_n, S, T2_1, ..., T2_m, S, ...>, S>` should be equal to `L<L<T1_1, T1_2, ..., T1_n>, L<T2_1, T2_2, ..., T2_m>, ...>>`  
  
Here's the implementation in brigand: https://github.com/edouarda/brigand/blob/master/include/brigand/algorithms/split.hpp

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-10 22:24:43 UTC  
> Updated at: 2021-03-10 22:27:52 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-796228200  

This looks fairly trivial, except for the case where S is not present in L at all. What is the behavior of brigand::split in this case? What do you need the behavior to be?

---

## Comment 2

> Username: u3shit  
> Created at: 2021-03-11 00:54:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-796335385  

Brigand just returns `L<L<T...>>` in that case, which looks reasonable for me.  
Actually a more interesting case is when L starts or ends with S and duplicate S. Now in brigand, `split<L<S, T...>, S>` is `L<L<T...>>`, and `split<L<T1, S, S, T2>, S>` is `L<L<T1>, L<T2>>`, but it might make more sense to leave empy `L<>`s in the output. So `split<L<S, T...>, S>` could be `L<L<>, L<T...>>` and `split<L<T1, S, S, T2>, S>` is `L<L<T1>, L<>, L<T2>>`. One advantage of this latter method that you can write a `join` that unambigously reconstructs the original list. (Actually I realized that my code already had a join implementation because that's missing even from brigand.)  
  
Now in my intended use case, `split<L<T...>, S>` should be `L<L<T...>>` when there is no S in T. The other cases, I don't care about them (they shouldn't happen in my case)

---

## Comment 3

> Username: pdimov  
> Created at: 2021-03-11 04:58:54 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-796453646  

Yeah, mp_join would be a natural companion. There's one more corner case: `mp_split<L<>, S>`, which can equally well be `L<>` or `L<L<>>`.

---

## Comment 4

> Username: u3shit  
> Created at: 2021-03-11 10:16:57 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-796627740  

Brigand does `L<>` in this case, but yeah, both of them is fine.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-03-12 17:23:00 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-797637390  

Implemented mp_split, mp_join on develop; since 1.76 is in beta, they will go into 1.77.

---

## Comment 6

> Username: u3shit  
> Created at: 2021-03-16 19:41:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/59#issuecomment-800552030  

Checked it out, looks OK. :+1:  
  
>will go into 1.77  
  
It looks like it'll take some time before I can remove my current workaround. Thanks anyway.
