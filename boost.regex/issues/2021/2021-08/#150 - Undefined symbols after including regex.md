# #150 - Undefined symbols after including regex [Closed]

> Username: jpalus  
> Created at: 2021-08-15 23:04:45 UTC  
> Updated at: 2021-10-13 17:01:51 UTC  
> Closed at: 2021-10-13 17:01:51 UTC  
> Url: https://github.com/boostorg/regex/issues/150  

I'm trying to rebuild software using boost regex against 1.76.0 but I get unresolved symbols. As far as I understand that comes from a discrepancy between implementation which is fully guarded by condition:  
https://github.com/boostorg/regex/blob/86f82635d4eb483be1b5cb72820dcf998cec48be/src/static_mutex.cpp#L22  
but header is not:  
https://github.com/boostorg/regex/blob/86f82635d4eb483be1b5cb72820dcf998cec48be/include/boost/regex/pending/static_mutex.hpp#L21-L43  
Header assumes implementation will be provided for constructor and destructor:  
https://github.com/boostorg/regex/blob/86f82635d4eb483be1b5cb72820dcf998cec48be/include/boost/regex/pending/static_mutex.hpp#L46-L47  
But they never are if BOOST_REGEX_CXX03 is not defined resulting in undefined symbols.  
  
Shouldn't condition in both header and implementation match?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-06 12:42:30 UTC  
> Url: https://github.com/boostorg/regex/issues/150#issuecomment-936163450  

That header is only ever used when BOOST_REGEX_CXX03 is defined.  
  
Are you including it directly?

---

## Comment 2

> Username: jpalus  
> Created at: 2021-10-06 23:40:42 UTC  
> Url: https://github.com/boostorg/regex/issues/150#issuecomment-937326639  

Looks like it is caused by different `-std=` used to compile `boost` and one used to compile software using `boost`. I see that the latter uses `-std=c++03` while `boost` compilation used newer version hence `boost` lacks symbols which `boost user` tries to use. Not sure if such combination is supported but if not it would be nice if header already gave `#error` at compilation time.  
  
In any case I'm no longer interested in getting said software to work hence feel free to close.
