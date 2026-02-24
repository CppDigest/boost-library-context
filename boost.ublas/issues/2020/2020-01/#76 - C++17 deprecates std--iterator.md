# #76 - C++17 deprecates std::iterator [Closed]

> Username: zvilius  
> Created at: 2020-01-22 00:13:40 UTC  
> Updated at: 2022-05-10 14:34:33 UTC  
> Closed at: 2020-08-12 20:19:55 UTC  
> Url: https://github.com/boostorg/ublas/issues/76  

Compiling with Visual Studio 2017 with c++17 language flag set:  
  
`warning C4996: 'std::iterator<IC,T,ptrdiff_t,_Ty *,_Ty &>::iterator_category': warning STL4015: The std::iterator class template (used as a base class to provide typedefs) is deprecated in C++17.  
`  
Results from using one of the iterator base classes, e.g. `random_access_iterator_base`.  
  
Not urgent as it is easy enough to suppress the warning, but I thought I'd note the issue since I don't see it here already.

---

## Comment 1

> Username: bassoy  
> Created at: 2020-01-22 06:00:35 UTC  
> Url: https://github.com/boostorg/ublas/issues/76#issuecomment-577022044  

thanks @zvilius . We will hopefully soon migrate to C++17 and use a different approach for iterating over matrix and tensor index spaces.

---

## Comment 2

> Username: jwakely  
> Created at: 2020-08-05 14:08:13 UTC  
> Url: https://github.com/boostorg/ublas/issues/76#issuecomment-669214901  

Looks like this is (partially?) fixed by #100

---

## Comment 3

> Username: bassoy  
> Created at: 2020-08-12 20:19:55 UTC  
> Url: https://github.com/boostorg/ublas/issues/76#issuecomment-673090118  

> Looks like this is (partially?) fixed by #100  
  
Yes. True.   
Yet, PR https://github.com/boostorg/ublas/pull/100 has not been merged.  
However, PR https://github.com/boostorg/ublas/pull/97 with the same changes has been merged into develop branch.

---

## Comment 4

> Username: SpareSimian  
> Created at: 2022-05-10 14:34:33 UTC  
> Url: https://github.com/boostorg/ublas/issues/76#issuecomment-1122480532  

With the recent discussion on the boost-devel list about establishing a minimum-supported C++ standard, I test-compiled my ublas-using C++14-compatible app with C++20 and got this warning. I suggest revisiting this upgrade to C++17.
