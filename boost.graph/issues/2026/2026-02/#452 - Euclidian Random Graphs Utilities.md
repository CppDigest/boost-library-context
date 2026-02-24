# #452 - Euclidian Random Graphs Utilities [Open]

> Username: megyhazy  
> Created at: 2026-02-01 00:31:20 UTC  
> Updated at: 2026-02-02 00:19:05 UTC  
> Url: https://github.com/boostorg/graph/issues/452  

As per discussion with Arnaud Becheler, he suggested I create an issue related to Euclidian Graph Utilities that I created as a side effect (part of examples/testing) of implementing Traveling Salesperson generators (metric_tsp, nearest_neighbor).    
  
These utilities allow the user to generate fully connected graphs based on configurable random distributions (normal, uniform, clusters, etc), scale (max x, max y), and number of points.  These kinds of graphs are useful in the context of TSP algorithms as many rely on fully connectedness and this allows for tuning of graph shape for testing and benchmarking purposes.  Utilities include:  
  
```  
template < typename OutputIterator, typename XDistribution,  
    typename YDistribution, typename RandomEngine >  
void generate_random_points(std::size_t num_points, XDistribution x_dist,  
    YDistribution y_dist, OutputIterator out, RandomEngine& rng)    
```  
  
```  
template < typename VertexListGraph, typename PointContainer,  
    typename WeightMap, typename VertexIndexMap >  
void connect_all_euclidean(VertexListGraph& g, const PointContainer& points,  
    WeightMap wmap, VertexIndexMap vmap)  
```  
  
```  
template < typename VertexListGraph, typename WeightMap,  
    typename VertexIndexMap, typename XDistribution, typename YDistribution >  
void make_random_euclidean_graph(VertexListGraph& g, std::size_t num_points,  
    XDistribution x_dist, YDistribution y_dist, WeightMap weight_map,  
    VertexIndexMap vertex_index_map)  
```

---

## Comment 1

> Username: Becheler  
> Created at: 2026-02-01 13:03:18 UTC  
> Updated at: 2026-02-01 13:13:59 UTC  
> Url: https://github.com/boostorg/graph/issues/452#issuecomment-3831020174  

Hi @megyhazy !  
  
I think adding some graph generators is a very good idea 💡   
  
Geometric graphs in particular have interest beyond TSP, for example I would be tempted to use them with the Louvain algorithm to benchmark uniform versus gaussian clusterings.  
  
It also acts as a nice bridge between real and messy graphs and existing topological BGL generators:  
  
- `erdos_renyi_generator.hpp`  
- `small_world_generator.hpp`  
- `mesh_graph_generator.hpp`  
- `plod_generator.hpp`  
- `rmat_graph_generator.hpp`  
- `ssca_graph_generator.hpp`  
  
Those have an iterator-based interface, but your case seems closer to `generate_random_graph()` (in `random.hpp`) that has a functional interface too.  
  
## Regarding file structures   
  
Here is what I noted:  
  
- All generators seem to be tested in `generator_test.cpp`  
- `generate_random_graph` seems to be used to tests algorithms but does not have its own test file  
- that leads me to think that `generator_test.cpp` is mostly dedicated to iterator-based interface ...  
- ... and that for your specific PR, the folowing files would have cleaner separation of concerns:  
    - `included/boost/graph/euclidian_graph_generator.hpp`  
    - `test/euclidian_graph_generator.cpp`   
    - `example/euclidian_graph_generator.cpp`  
    - `doc/euclidean_graph_generator.html`  
  
## Regarding the example file  
  
Existing generators only have HTML documentation and tests, but I think a minimal standalone example would make geometric graphs much more accessible. Geometric graphs come with spatial intuition of their vertices spatial distribution that is lost in pure API documentation.   
So your example code you showed me, demonstrating uniform vs Gaussian point distributions, would make the concept intuitive and would lower the bar for beginners. This could actually set a good precedent for better generator documentation going forward 🥲   
  
What do you think ? We can also ask @jeremy-murphy for confirmation 😸

---

## Comment 2

> Username: megyhazy  
> Created at: 2026-02-02 00:19:05 UTC  
> Url: https://github.com/boostorg/graph/issues/452#issuecomment-3832353041  

Ok, agreed on all fronts.  Will work on this.  Thanks!
