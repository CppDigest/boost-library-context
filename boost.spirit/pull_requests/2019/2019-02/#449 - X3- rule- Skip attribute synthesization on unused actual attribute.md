# #449 X3: rule: Skip attribute synthesization on unused actual attribute [Merged]

> Username: Kojoley  
> Created at: 2019-02-03 02:19:47 UTC  
> Updated at: 2019-02-06 13:42:11 UTC  
> Merged at: 2019-02-06 13:42:08 UTC  
> Closed at: 2019-02-06 13:42:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/449  

There is a mistake in `make_attribute` trait that leads to synthesization and  
immediately throwing out an attribue, so rule context always was unused_type  
when actual attribute is unused_type (added test for this case). Instead of  
fixing `make_attribute` trait I just completely removed synthesization in  
rule by removing `make_attribute` trait usage (the traits could be removed  
after a simple change in 'action' parser). I think the change is better  
than fixing the trait because it removes excessive attribute default  
construction in every rule and makes rule almost free to use.

---
