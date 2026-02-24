# #528 - Add support for pmr::polymorphic_allocator [Closed]

> Username: sdebionne  
> Created at: 2020-11-06 10:00:38 UTC  
> Updated at: 2022-06-28 06:35:28 UTC  
> Closed at: 2022-06-28 06:35:28 UTC  
> Url: https://github.com/boostorg/gil/issues/528  

### Is your feature request related to a problem? Please describe.  
  
Using `gil::image<..., std::pmr::polymorphic_allocator<unsigned char>>` fails because the copy assignment operator uses `swap` internally, which is good practice, but  `polymorphic_allocator ` is not Swappable. Are we supposed to swap the allocators?  
  
### Describe the solution you'd like  
  
`gil::image<..., std::pmr::polymorphic_allocator<unsigned char>>` should compile and works as expected.  
  
#### C++ Example  
  
I can provide an extension of the test suite for this use case. Note that copy assignment operator and copy constructors needs to be added to the tests as well to reproduce the issue with `swap`.
