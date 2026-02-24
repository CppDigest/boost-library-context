# #47 - Add a BCrypt abstraction [Closed]

> Username: jeking3  
> Created at: 2017-09-14 01:01:37 UTC  
> Updated at: 2017-10-07 18:14:07 UTC  
> Closed at: 2017-10-07 18:14:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/47  

This is an issue that surfaced in the uuid project, where seed_rng does not work with UWP non-desktop.  To support newer Windows platforms, the random project will need access to bcrypt.  BCrypt will be used by future boostorg/random for all platforms at Windows 7 or later in the random_device code.  BCrypt is also available in MinGW-x64, but it is not available in MinGW.
