# #336 - Wrong CGG expansion of BOOST_ATTRIBUTE_NODISCARD when in -std=c++98 mode [Closed]

> Username: joaquintides  
> Created at: 2020-05-10 12:40:38 UTC  
> Updated at: 2021-08-20 07:00:35 UTC  
> Closed at: 2021-08-19 18:22:02 UTC  
> Url: https://github.com/boostorg/config/issues/336  

For GCC>7.0 and C++03 mode (`-std=c++98`), `BOOST_ATTRIBUTE_NODISCARD` wrongly expands to `[[nodiscard]]` (it should expand to nothing).

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-08-19 16:00:19 UTC  
> Url: https://github.com/boostorg/config/issues/336#issuecomment-902034267  

Apologies for the long delay!  
  
This appears to be deliberate - `[[nodiscard]]` appears to be accepted by gcc without warning or error in all std modes.  
  
Can you explain what issue this is causing?

---

## Comment 2

> Username: joaquintides  
> Created at: 2021-08-19 16:15:37 UTC  
> Url: https://github.com/boostorg/config/issues/336#issuecomment-902046460  

No apologies needed :-)  
  
I see otherwise: https://godbolt.org/z/P4dnd4T8q

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-08-19 17:18:45 UTC  
> Url: https://github.com/boostorg/config/issues/336#issuecomment-902096219  

Confirmed, works on classes but not functions.  
See https://github.com/boostorg/config/pull/398.

---

## Comment 4

> Username: joaquintides  
> Created at: 2021-08-19 17:43:57 UTC  
> Updated at: 2021-08-20 07:00:35 UTC  
> Url: https://github.com/boostorg/config/issues/336#issuecomment-902114881  

Perfect, thank you! Will keep an eye to this so that I can drop my workaround when your fix gets to master.
