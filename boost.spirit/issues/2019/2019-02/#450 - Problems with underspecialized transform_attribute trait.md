# #450 - Problems with underspecialized transform_attribute trait [Closed]

> Username: Kojoley  
> Created at: 2019-02-03 13:21:37 UTC  
> Updated at: 2019-03-08 02:07:46 UTC  
> Closed at: 2019-03-08 02:07:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/450  

Recently a lot of problems with `make_attribute` and `transform_attribute` traits were uncovered at fixing #444. I think the problem is that the trait does not have constrains, example: https://github.com/boostorg/spirit/blob/4b2b443e58ad975f9085b04db220ac6bdd79901b/include/boost/spirit/home/x3/nonterminal/detail/transform_attribute.hpp#L102-L107  
Because template parameter behind `attr` is a forwarding reference `Transformed` will be reference, but `transform_attribute` does not expect `Transformed` to be a reference and even const qualifier.  
  
  - [x] Add static assertions to catch passing references <strike>and const types</strike> to `Transformed` parameter.  
  - [x] Remove `traits::pre_transform` and `traits::post_transform` traits to be sure both pre and post uses the same transformation trait.  
  - [x] Possibly forbid references in `Exposed` parameter.  
  
Update: It is not clear to me if const can never appear in `Transformed`, so will do not touch as for now.
