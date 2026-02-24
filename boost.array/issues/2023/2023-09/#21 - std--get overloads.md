# #21 - std::get overloads? [Open]

> Username: pdimov  
> Created at: 2023-09-08 07:05:58 UTC  
> Updated at: 2023-09-08 07:05:58 UTC  
> Url: https://github.com/boostorg/array/issues/21  

Defining overloads in `namespace std` is undefined behavior, so I'm wondering why we're adding overloads of `std::get` for `boost::array`. Since we aren't specializing `std::tuple_size` or `std::tuple_element`, these overloads would never be used anyway, even if they were legal.
