# #447 - [FEATURE] Louvain/Leiden Algorithm for Community Detection [Open]

> Username: Becheler  
> Created at: 2026-01-10 22:14:13 UTC  
> Updated at: 2026-02-13 15:16:27 UTC  
> Url: https://github.com/boostorg/graph/issues/447  

Hi Graphies,  
  
## Current state  
  
If I understand correctly there is currently no method for community detection in BGL (but see clustering methods). Community detection algorithms like Louvain and Leiden automatically infer hidden groupings in very large networks, identifying gene modules, customer segments, fraud rings, or system components without requiring labeled data.  
Common methods include:  
- K-core Decomposition  
- Label Propagation  
- the Louvain algorithm (popular, ~30000 citations, some statistical issues)  
- the Leiden algorithm (extends Louvain, solves some issues, more complex, less standard ~ 6000 citations)  
  
## Scope  
  
I plan to submit a PR to implement the simplest case of Louvain modularity optimization for undirected graphs only:  
- minimal BGL-style API  
- regression + correctness tests to reproduce validation methods/results in Blondel et al 2008  
- no metric generalization yet  
  
The benefit of starting with Louvain is that it builds requried components for Leiden, that is also more complex.  
  
<img width="968" height="618" alt="Image" src="https://github.com/user-attachments/assets/41374d6b-e66c-4e0f-bf6a-9cdb2824eb0b" />  
Algorithm figure from Blondel at al. 2008  
  
## Litterature and context  
  
Related papers and ressource:  
- [Newman and Girvan, 2004](https://arxiv.org/pdf/cond-mat/0308217): paper for modularity metrics, with per-community version too  
- [Blondel et al, 2008](https://arxiv.org/pdf/0803.0476): Louvain foundational paper using modularity metrics  
- [Campigotto et al, 2014](https://arxiv.org/pdf/1406.2518): Generalizing Louvain to multiple quality metrics + C++ implementation using Strategy pattern (see reference 4)  
- [Traag et al, 2019](https://www.traag.net/wp/wp-content/papercite-data/pdf/traag_leiden_algo_2018.pdf) : Leiden paper + Louvain background  
- [Louvain Method CS class video](https://www.youtube.com/watch?v=QfTxqAxJp0U)  
- [Comparative evaluation of community detection methods ](https://hal.science/hal-01976587/document) : methods, performances, generated community sizes distributions  
  
## Formalism  
  
Modularity defined as:  
```math  
Q = \frac{1}{2m} \sum_{ij} \left[ A_{ij} - \gamma \frac{k_i k_j}{2m} \right] \delta(c_i, c_j)  
```  
Where:  
 - $m$ is the sum of all edges weights  
- $A$ is the adjacency matrix  
- $\gamma$ is the resolution parameter  
- $k_i$ is the weighted degree of vertex $i$  
- the $\delta$ function is 1 if $i$ and $j$ are in the same community, else 0  
  
It is defined to be equivalent to the community-level formula used for computation:  
```math  
Q = \sum_{c=1}^{n}  \left[  \frac{L_c}{m} - \gamma \left( \frac{k_c}{2m} \right) ^2 \right]  
```  
Where:  
- $n$ is the number of communities$  
- $L_c$ is the number of intra-community edges for community $c$  
- $k_c$ is the sum of degrees of the vertices in community $c$  
- $\gamma$ is the resolution parameter, often equal to $1$  
  
The optimization efficiency comes from the possibility to compute the gain in modularity when moving a node $i$ from its community $c_i$ to the community of one of its neighbors $c_j$ without recomputing the full graph:  
  
```math  
\Delta Q = \left[ \frac{\sum_{in} + k_{i,in}}{2m} - \left( \frac{ \sum_{tot} + k_i}{2m} \right)^2   \right]  - \left[ \frac{\sum_{in}}{2m} -  \left( \frac{ \sum_{tot}}{2m} \right)^2 - \left( \frac{k_i}{2m} \right)^2 \right]   
```  
Where:  
- $\sum_{in}$ is the sum of the weights of the links inside $C$,  
- $\sum_{tot}$ is the sum of the weights of the links incident to nodes in $C,   
- $k_i$ is the sum of the weights of the links incident to node $i$,   
- $k_{i,in}$ is the sum of the weights of the links from $i$ to nodes in $C$  
- $m$ is the sum of the weights of all the links in the network  
  
## Possible implementation  
  
I will just throw a quick idea of the interface just to be sure it fits well wit BGL spirit  
  
```cpp  
template <typename Graph, typename CommunityMap, typename WeightMap>  
double compute_modularity(const Graph& g, const CommunityMap& communities, const WeightMap& weights)  
{  
    // probably needs vertices(), out_edges(), target(), source()  
    BOOST_CONCEPT_ASSERT((boost::IncidenceGraphConcept<Graph>));  
    BOOST_CONCEPT_ASSERT((boost::VertexListGraphConcept<Graph>));  
      
    // directed louvain seems to require modifsm, see https://github.com/nicolasdugue/DirectedLouvain  
    typedef typename boost::graph_traits<Graph>::directed_category directed_category;  
    BOOST_STATIC_ASSERT_MSG(  
        (boost::is_same<directed_category, boost::undirected_tag>::value),  
        "compute_modularity requires an undirected graph"  
    );  
      
    // impl from Blondel 2008 works with weighted graphs  
}  
  
template <typename Graph, typename CommunityMap, typename WeightMap>  
void louvain_clustering(const Graph& g, CommunityMap& communities, const WeightMap&) // note the write param  
{  
    BOOST_CONCEPT_ASSERT((boost::IncidenceGraphConcept<Graph>));  
    BOOST_CONCEPT_ASSERT((boost::VertexListGraphConcept<Graph>));  
      
    typedef typename boost::graph_traits<Graph>::directed_category directed_category;  
    BOOST_STATIC_ASSERT_MSG(  
        (boost::is_same<directed_category, boost::undirected_tag>::value),  
        "louvain_clustering requires an undirected graph."  
    );  
      
    // hide state from user ?  
    std::vector<double> internal_edges_per_community(num_vertices(g));  
    std::vector<double> total_degree_per_community(num_vertices(g));  
    std::vector<double> neigh_community_weight(num_vertices(g));  
      
    // impl  
  
    // return void like djikstra ? or an enriched structured with internal results from the algo for debug ?  
}  
  
// unweighted overload  
template<typename Graph, typename CommunityMap>  
void louvain_clustering(const Graph& g, CommunityMap& communities)  
{  
   louvain_clustering(g, communities, boost::make_constant_property_map(1.0));  
}  
  
// usage:  
louvain_clustering(g, comm_map);  
double quality = compute_modularity(g, comm_map);  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2026-01-14 23:05:00 UTC  
> Url: https://github.com/boostorg/graph/issues/447#issuecomment-3752111909  

I think your initial decomposition into `louvain_clustering` and `compute_modularity` is good to start with. It can be tempting to do a perfect decomposition right from the start, but it's often better (and more efficient) to get a useful algorithm implemented, maybe even a few implemented, and let further decomposition and refinement emerge.  
  
Don't return something just for the sake of debugging -- return it because information computed can be useful to a subsequent algorithm or in an unanticipated use case. In this case, I guess it would be the "internal state" in those vectors. Can the information in them be efficiently inferred from the main result of the algorithm? If so, then there is no need to return them. But if they contain details that it would require non-trivial work to compute, then it might be important to return, or even consider making them an intermediate algorithm that creates the inputs for the main idea, possibly being reused by other algorithms in the future.  
  
If only all algorithm proposals were this well presented. ❤️

---

## Comment 2

> Username: Becheler  
> Created at: 2026-01-15 10:41:35 UTC  
> Updated at: 2026-01-15 11:38:13 UTC  
> Url: https://github.com/boostorg/graph/issues/447#issuecomment-3754110718  

@jeremy-murphy  thank you for your advices !  
I'm about to write the tests for the modularity metrics of graphs partitions: is there any convention about naming tests files ? I feel there is a bit of everything ?   
```  
Pattern 1: feature_test.cpp  
weighted_matching_test.cpp  
strong_components_test.cpp  
  
Pattern 2: feature.cpp (older style ?)  
bfs.cpp  
dfs.cpp  
dijkstra.cpp  
```  
Any preference ?

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2026-01-15 11:57:21 UTC  
> Url: https://github.com/boostorg/graph/issues/447#issuecomment-3754379984  

I'm a minimalist, so if a source file of tests for foo is in a test directory, then I just call it foo.cpp. But I don't really mind.
