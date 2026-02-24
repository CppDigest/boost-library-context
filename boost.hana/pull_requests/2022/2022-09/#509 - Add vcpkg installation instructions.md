# #509 Add vcpkg installation instructions [Open]

> Username: FrankXie05  
> Created at: 2022-09-01 09:25:15 UTC  
> Updated at: 2022-09-01 09:26:01 UTC  
> Url: https://github.com/boostorg/hana/pull/509  

`hana` is available as a port in [vcpkg](https://github.com/microsoft/vcpkg), a C++ library manager that simplifies installation for `hana` and other project dependencies. Documenting the install process here will help users get started by providing a single set of commands to build `hana`, ready to be included in their projects.  
  
We also test whether our library ports build in various configurations (dynamic, static) on various platforms (OSX, Linux, Windows: x86, x64) to keep a wide coverage for users.  
  
I'm a maintainer for vcpkg, and [here is what the port script looks like](https://github.com/microsoft/vcpkg/blob/master/ports/boost-hana/portfile.cmake). We try to keep the library maintained as close as possible to the original library.😊

---
