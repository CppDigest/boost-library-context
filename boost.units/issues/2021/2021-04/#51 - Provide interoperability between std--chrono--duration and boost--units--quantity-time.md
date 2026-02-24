# #51 - Provide interoperability between std::chrono::duration and boost::units::quantity<time> [Open]

> Username: jpo234  
> Created at: 2021-04-12 09:00:29 UTC  
> Updated at: 2021-04-13 03:04:45 UTC  
> Url: https://github.com/boostorg/units/issues/51  

The time dimension is special in C++, because there already is an implementation in the standard libraries: `std::chrono::duration`.  
I think boost::units should recognize this and provide an easy way to convert between these two different implementations of what is basically the same thing.  
Ideally one should be able to do:  
`quantity<time> q = 2h;`  
where 2h is a `std::literals::chrono_literals::operator""h`.

---

## Comment 1

> Username: correaa  
> Created at: 2021-04-13 03:03:57 UTC  
> Updated at: 2021-04-13 03:04:45 UTC  
> Url: https://github.com/boostorg/units/issues/51#issuecomment-818396416  

Yes, one problem is that chrono is based on integer value types while Boost.Units, is generally based around floating point values. But I would also like something in this direction.
