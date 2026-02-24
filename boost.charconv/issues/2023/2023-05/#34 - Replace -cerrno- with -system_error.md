# #34 - Replace <cerrno> with <system_error> [Closed]

> Username: mborland  
> Created at: 2023-05-12 10:11:27 UTC  
> Updated at: 2023-05-18 11:30:35 UTC  
> Closed at: 2023-05-18 11:30:35 UTC  
> Url: https://github.com/boostorg/charconv/issues/34  

Instead of `int ec` the structs should have `std::errc ec`.
