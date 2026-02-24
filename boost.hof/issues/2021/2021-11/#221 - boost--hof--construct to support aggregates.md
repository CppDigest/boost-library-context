# #221 - boost::hof::construct to support aggregates [Open]

> Username: Aster89  
> Created at: 2021-11-17 17:20:12 UTC  
> Updated at: 2021-11-17 17:20:12 UTC  
> Url: https://github.com/boostorg/hof/issues/221  

I've discovered `BOOST_HOF_LIFT`/`BOOST_HOF_LIFT_CLASS` [some time ago](https://stackoverflow.com/a/68357266/5825294), and it's very useful when I want to make an object out of the name of a templated and/or overloaded function.  
  
Then I've discovered `boost::hof::construct`, which helps me avoid writing `[](auto x){ return Ctor{x}; }` all over the place.  
  
But almost immediately [I run into the issue that `boost::hof::construct` can't be used with aggregate types](https://stackoverflow.com/questions/69993346/does-boost-or-another-library-offer-a-way-to-lift-the-name-of-a-constructor-l) (sorry if I'm misusing the term, I mean those that don't have a user defined constructor; if I understand [this](https://stackoverflow.com/a/4178176/5825294), that's what an aggregate is, but correct me if I'm wrong).  
  
I know that _HigherOrderFunctions is a header-only C++11/C++14 library_, but the point is that using class names as object is not possible in those standards, but not even in C++17, or C++20, for what is worth, so `BOOST_HOF_LIFT`/`BOOST_HOF_LIFT_CLASS`/`boost::hof::construct` are still all useful in those newer standards.  
  
So my bottom line is, why not upgrading `boost::hof::construct` so that it can work with aggregate types too (in C++17, where `std::is_aggregate`) is available?
