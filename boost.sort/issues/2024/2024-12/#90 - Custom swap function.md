# #90 - Custom swap function? [Open]

> Username: bog-dan-ro  
> Created at: 2024-12-16 09:25:02 UTC  
> Updated at: 2024-12-17 18:44:09 UTC  
> Url: https://github.com/boostorg/sort/issues/90  

Hello,  
  
It will be great if these algorithms allow setting a custom swap function.  
In my use-case  besides swapping the values I need to do other work (on my qt application I need to fire also signals).

---

## Comment 1

> Username: spreadsort  
> Created at: 2024-12-17 01:30:01 UTC  
> Url: https://github.com/boostorg/sort/issues/90#issuecomment-2547307331  

On Mon, Dec 16, 2024 at 4:25 AM BogDan Vatra ***@***.***>  
wrote:  
  
> Hello,  
>  
> It will be great if these algorithms allow setting a custom swap function.  
> In my use-case besides swapping the values I need to do other work (on my  
> qt application I need to fire also signals).  
>  
  
pdqsort and string_sort use iter_swap, which should allow overrides.  Which  
algorithm did you want to use?  
integer_sort has a 3-way swap which you can't override because it provides  
a 1% speedup (4 assignments to move 3 elements, as opposed to 3 assignments  
to move 2).  
  
  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/90>, or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABG2HXAWGGMJHBZ6FHJDQPL2F2MAJAVCNFSM6AAAAABTVWAIWWVHI2DSMVQWIX3LMV43ASLTON2WKOZSG42DCOBRGY3TMNQ>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>

---

## Comment 2

> Username: bog-dan-ro  
> Created at: 2024-12-17 08:15:42 UTC  
> Url: https://github.com/boostorg/sort/issues/90#issuecomment-2547765890  

Personally I use sample_sort,parallel_stable_sort & flat_stable_sort. But is not about me :).  
IMHO all (possible) algorithms should support passing a `swap` function.  
Be ware that the user needs to pass a function (e.g. a lambda) as it needs access to other data.

---

## Comment 3

> Username: fjtapia  
> Created at: 2024-12-17 18:44:07 UTC  
> Url: https://github.com/boostorg/sort/issues/90#issuecomment-2549316118  

First, thank you for your interest in the library.  
Changing the format of the functions is a problem because many people use  
them. Perhaps adding an additional format can be a solution.  
But you have a trivial solution. Derive your class, and in the derived  
class define the function swap and all the functions you need. The size of  
your class and the size of the derived class are equal. You sort a vector  
or elements of the derived class  
  
El mar, 17 dic 2024 a las 9:16, BogDan Vatra ***@***.***>)  
escribió:  
  
> Personally I use sample_sort,parallel_stable_sort & flat_stable_sort. But  
> is not about me :).  
> IMHO all (possible) algorithms should support passing a swap function.  
> Be ware that the user needs to pass a function (e.g. a lambda) as it needs  
> access to other data.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/sort/issues/90#issuecomment-2547765890>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA5O5GCCQ5Q4W4ZU4QHAVE32F7MULAVCNFSM6AAAAABTVWAIWWVHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDKNBXG43DKOBZGA>  
> .  
> You are receiving this because you are subscribed to this thread.Message  
> ID: ***@***.***>  
>
