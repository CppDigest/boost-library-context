# #225 Replace some uses of boost::is_same with std::is_same [Merged]

> Username: mloskot  
> Created at: 2019-01-27 01:17:38 UTC  
> Updated at: 2019-01-27 10:54:39 UTC  
> Merged at: 2019-01-27 10:54:06 UTC  
> Closed at: 2019-01-27 10:54:06 UTC  
> Url: https://github.com/boostorg/gil/pull/225  

Those are places boost::is_same can be replaced in non-intrusive way.  
Remaining are types derived from `boost::is_same`, where the replacing  
would affect all their uses - effectively switching from `boost::true_/false_` types  
to `std::true_type` and `std::false_type`.  
Such change needs to come along with Boost.MPL to MP11 migration.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
