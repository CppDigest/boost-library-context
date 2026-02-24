# #132 - [Constant] Consider providing an easier way to fulfill the MCD [Closed]

> Username: ldionne  
> Created at: 2015-06-17 14:02:48 UTC  
> Updated at: 2015-09-09 23:37:02 UTC  
> Closed at: 2015-09-09 23:37:02 UTC  
> Url: https://github.com/boostorg/hana/issues/132  

Another option would be to simplify the MCD so it uses the nested `::value` inside an object's type. This way, `std::integral_constant`s might work out of the box. However, we have to be careful not to lose the ability to have non-integral `Constant`s, since that's the whole purpose of that concept. An automatic model for things that look enough like a `std::integral_constant` might be OK.
