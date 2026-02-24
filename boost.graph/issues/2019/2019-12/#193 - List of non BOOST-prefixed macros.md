# #193 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 14:09:23 UTC  
> Updated at: 2019-12-25 00:48:29 UTC  
> Closed at: 2019-12-25 00:48:29 UTC  
> Url: https://github.com/boostorg/graph/issues/193  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/graph/minimum_degree_ordering.hpp:#ifndef MINIMUM_DEGREE_ORDERING_HPP  
./boost/graph/minimum_degree_ordering.hpp:#define MINIMUM_DEGREE_ORDERING_HPP  
./boost/graph/minimum_degree_ordering.hpp:#endif // MINIMUM_DEGREE_ORDERING_HPP  
./boost/graph/edmonds_karp_max_flow.hpp:#ifndef EDMONDS_KARP_MAX_FLOW_HPP  
./boost/graph/edmonds_karp_max_flow.hpp:#define EDMONDS_KARP_MAX_FLOW_HPP  
./boost/graph/edmonds_karp_max_flow.hpp:#endif // EDMONDS_KARP_MAX_FLOW_HPP  
./boost/graph/adj_list_serialize.hpp:#ifndef ADJ_LIST_SERIALIZE_HPP  
./boost/graph/adj_list_serialize.hpp:#define ADJ_LIST_SERIALIZE_HPP  
./boost/graph/adj_list_serialize.hpp:#endif // ADJ_LIST_SERIALIZE_HPP  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2019-12-25 00:48:29 UTC  
> Url: https://github.com/boostorg/graph/issues/193#issuecomment-568815083  

Fixed in 2159a9ec629372fb89db3c4e6bf68db5791dd7d5.
