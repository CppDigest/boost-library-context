# #198 - bron_kerbosch_all_cliques unused variable [Closed]

> Username: dancooke  
> Created at: 2020-01-20 15:04:09 UTC  
> Updated at: 2026-02-18 12:21:31 UTC  
> Closed at: 2026-02-18 12:21:31 UTC  
> Url: https://github.com/boostorg/graph/issues/198  

Using [`bron_kerbosch_all_cliques`](https://www.boost.org/doc/libs/1_72_0/boost/graph/bron_kerbosch_all_cliques.hpp) results in an unused variable warning  
  
```  
/usr/local/include/boost/graph/bron_kerbosch_all_cliques.hpp:227:41: error: unused variable 'j' [-Werror,-Wunused-variable]  
        typename Container::iterator i, j;  
                                        ^  
/usr/local/include/boost/graph/bron_kerbosch_all_cliques.hpp:289:13: note: in instantiation of function template specialization 'boost::detail::extend_clique<boost::adjacency_matrix<boost::undirectedS, unsigned long, octopus::Phred<double, void>, boost::no_property, std::__1::allocator<bool> >, std::__1::deque<unsigned long, std::__1::allocator<unsigned long> >, std::__1::vector<unsigned long, std::__1::allocator<unsigned long> >, octopus::MaxCliqueVisitor<boost::adjacency_matrix<boost::undirectedS, unsigned long, octopus::Phred<double, void>, boost::no_property, std::__1::allocator<bool> > > >' requested here  
    detail::extend_clique(g, clique, cands, nots, vis, min);  
            ^  
/usr/local/include/boost/graph/bron_kerbosch_all_cliques.hpp:297:3: note: in instantiation of function template specialization 'boost::bron_kerbosch_all_cliques<boost::adjacency_matrix<boost::undirectedS, unsigned long, octopus::Phred<double, void>, boost::no_property, std::__1::allocator<bool> >, octopus::MaxCliqueVisitor<boost::adjacency_matrix<boost::undirectedS, unsigned long, octopus::Phred<double, void>, boost::no_property, std::__1::allocator<bool> > > >' requested here  
{ bron_kerbosch_all_cliques(g, vis, 2); }  
```  
  
Looks like `j` can simply be removed?

---

## Comment 1

> Username: andreacassioli  
> Created at: 2026-02-17 20:47:37 UTC  
> Url: https://github.com/boostorg/graph/issues/198#issuecomment-3917012724  

This issue is solved, i.e. the mention variable is not there.
