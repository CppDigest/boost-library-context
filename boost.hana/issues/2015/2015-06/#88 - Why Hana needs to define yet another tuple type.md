# #88 - [Question] Why Hana needs to define yet another tuple type? [Closed]

> Username: viboes  
> Created at: 2015-06-08 17:38:31 UTC  
> Updated at: 2015-06-13 13:21:59 UTC  
> Closed at: 2015-06-08 20:02:33 UTC  
> Url: https://github.com/boostorg/hana/issues/88  

Is there any restriction on the std::tuple type that prevents Hana to use it?  
Could std::tuple be integrated in Hana?  
  
I see   
  
``` c++  
Cat garfield = animals[1_c];  
```  
  
But is tis enough?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-08 17:41:57 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-110089033  

It is, but it is not documented yet. Note that `std::pair`, `std::ratio`, `std::integral_constant` and `std::integer_sequence` are also adapted and can be used with Hana. However, `std::tuple` in libc++ has a couple of bugs that makes it not 100% usable with Hana, but that's not a fundamental problem.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-06-08 18:10:05 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-110094812  

> I see  
>   
> ``` c++  
>  Cat garfield = animals[1_c];  
> ```  
>   
> But is tis enough?  
  
I don't understand your updated question.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-08 18:15:20 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-110095857  

Oh; are you asking whether I think providing sugar like `animals[1_c]` is enough to mandate creating a new tuple type? The reasons for providing a new tuple type are deeper than that. I'd say the most important one is performance; `std::tuple` implementations are usually very slow at compile-time. We can also apply interesting compile-time optimizations in algorithms if we control the exact representation of the tuple. Finally, there's the sugar.

---

## Comment 4

> Username: ldionne  
> Created at: 2015-06-08 18:16:14 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-110096028  

But if you prefer, you can also use `std::tuple` via the provided adapter. However, beware of slow compile-times :-).

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-08 20:02:33 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-110121594  

I'll assume this answers your question and close this. If it doesn't please let me know.

---

## Comment 6

> Username: viboes  
> Created at: 2015-06-13 13:21:59 UTC  
> Url: https://github.com/boostorg/hana/issues/88#issuecomment-111709872  

Thanks Louis for the answer. I would appreciate if the rationale is explained in the documentation.
