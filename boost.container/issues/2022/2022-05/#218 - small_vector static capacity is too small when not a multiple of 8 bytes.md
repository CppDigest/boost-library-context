# #218 - small_vector static capacity is too small when not a multiple of 8 bytes [Closed]

> Username: wthrowe  
> Created at: 2022-05-25 17:50:14 UTC  
> Updated at: 2022-07-10 16:36:22 UTC  
> Closed at: 2022-07-10 16:36:22 UTC  
> Url: https://github.com/boostorg/container/issues/218  

It appears that (except for space smaller than 8 bytes), the size of the static allocation in `small_vector<T, N>` is rounded down to the nearest 8 bytes, which, for objects less than 8 bytes in size, means that fewer than N can be stored in the static space.  
  
Godbolt link showing that, for example, `small_vector<char, 15>` has an initial capacity of 8 and resizing to 15 objects causes reallocation: https://godbolt.org/z/5Po56jbo9

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-10 16:35:33 UTC  
> Url: https://github.com/boostorg/container/issues/218#issuecomment-1179759819  

Thanks for the report. It looks like GCC can reuse padding bytes of base classes to store elements, so the logic used by small vector was not correct. I've figured out another implementation that seems to do it better. Now your test prints:  
  
> 0 0 24  
1 1 24  
2 2 24  
3 3 24  
4 4 24  
5 5 24  
6 6 24  
7 7 24  
8 8 24  
9 9 24  
10 10 24  
11 11 24  
12 12 24  
13 13 24  
14 14 24  
15 15 24  
16 16 24  
17 17 24  
18 18 24  
19 19 24  
20 20 24  
21 21 24  
22 22 24  
23 23 24  
24 24 24
