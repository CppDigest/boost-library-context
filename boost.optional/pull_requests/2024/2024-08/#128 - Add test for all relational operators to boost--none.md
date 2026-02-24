# #128 Add test for all relational operators to boost::none [Merged]

> Username: Flamefire  
> Created at: 2024-08-16 09:21:42 UTC  
> Updated at: 2024-08-25 12:45:12 UTC  
> Merged at: 2024-08-16 18:15:55 UTC  
> Closed at: 2024-08-16 18:15:55 UTC  
> Url: https://github.com/boostorg/optional/pull/128  

I was inspecting the recent [change](https://github.com/boostorg/optional/commit/3a8556186ebc778176863b01060043d6aa1d3cc0) as it introduced a failure in my tests due to the new warning, see #127   
  
Some comparisons made me suspicious:  
  
E.g.  
```  
bool operator <= ( optional<T> const& x, none_t y )  
{ return !( y < x ) ; }  
bool operator >= ( optional<T> const& x, none_t y )  
{ return !( x < y ) ; }  
bool operator > ( optional<T> const& x, none_t y )  
{ return y < x ; }  
bool operator > ( none_t x, optional<T> const& y )  
{ return y < x ; }  
```  
  
Basically: Sometimes it is implemented swapping the parameters, sometimes using negation and different operator.  
  
As it wasn't obvious to me that they are correct and don't lead to infinite loops I added tests to verify them against the documentation. All is correct (as expected by experience) but the test might be useful anyway.

---
