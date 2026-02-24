# #166 Restructure the documentation of containers [Merged]

> Username: ldionne  
> Created at: 2015-08-29 20:22:40 UTC  
> Updated at: 2015-08-31 21:30:44 UTC  
> Merged at: 2015-08-31 21:25:07 UTC  
> Closed at: 2015-08-31 21:25:07 UTC  
> Url: https://github.com/boostorg/hana/pull/166  

The goal of this pull request is to document the actual types of containers (`hana::tuple`, `hana::set`, etc..) instead of documenting their tag only (`hana::Tuple`, `hana::Set`, etc..). In cases where the implementation of the container is really implementation defined, we shall document it as such (e.g. `hana::set<...>` should be documented, but its definition should be marked as implementation-defined). This was triggered by some comments by (IIRC) @pfultz2 and @viboes during and after the formal review.  
  
Also, documenting the actual types of containers is important if we want to close #157, since we will need a way to refer to the containers without using their tag: It would make no sense to say "let `x` be a `tuple_tag`, whereas it makes sense to say "let `x` be a `Tuple`" right now. If we fix #157, we'll need to refer to the containers using some other type, and I think the proper way to go is to document their actual type but to leave it implementation-defined in some cases.  
  
Documenting the actual type of containers will also provide a nice place where the type requirements of the elements of a container and the member functions of a container can be documented, thus addressing #116 and #163.  
  
Finally, this provides a good context in which #122 can be addressed, since we will have to fix the names of the containers's types if we want to make them part of the public interface. For example, this pull request renames the `range_t<...>` type to `range<...>`, and removes the deprecated `range(...)` function object. Similar actions should be taken for other containers, thus cleaning up the names of types and constructor functions, and addressing #122.  
  
Still left to do:  
- [x] Make it clear which containers have an implementation-defined representation, and what does that mean for users.  
- [x] Document `hana::lazy` (have to find another name) instead of `hana::Lazy`  
- [x] Document `hana::type` (actually, what exactly?) instead of `hana::Type`  
- [x] Find a way to document the `Metafunction` data type, too

---
