# #126 - boost.sort header conflicts with boost.range header [Open]

> Username: BenFrantzDale  
> Created at: 2021-03-25 16:54:26 UTC  
> Updated at: 2023-07-22 09:06:35 UTC  
> Url: https://github.com/boostorg/range/issues/126  

See https://svn.boost.org/trac10/ticket/11202 which seems to be abandoned.  
  
In `boost/range/algorithm/sort.hpp` (https://github.com/boostorg/range/blob/develop/include/boost/range/algorithm/sort.hpp#L65) there's  
```  
    } // namespace range  
    using range::sort;  
} // namespace boost  
```  
which pollutes the `::boost` namespace with `sort`. But `::boost::sort` is a namespace used here: https://github.com/boostorg/sort/blob/develop/include/boost/sort/heap_sort/heap_sort.hpp#L25  
  
This means I can't use both `boost::sort` and `boost::range::sort`.  
  
This appears to violate https://svn.boost.org/trac10/wiki/Guidelines/MaintenanceGuidelines#Avoidtheinclusionofsymbolsattheboostorboost::detailnamespace

---

## Comment 1

> Username: olologin  
> Created at: 2023-07-22 09:06:34 UTC  
> Url: https://github.com/boostorg/range/issues/126#issuecomment-1646536127  

Please fix this
