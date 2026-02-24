# #436 - Strange result of maximum_weighted_matching [Closed]

> Username: EGurylev  
> Created at: 2025-08-20 15:16:31 UTC  
> Updated at: 2025-08-25 05:37:47 UTC  
> Closed at: 2025-08-25 05:37:47 UTC  
> Url: https://github.com/boostorg/graph/issues/436  

**Version of Boost**  
  
1.86.0  
  
For example:  
```  
int main()  
{  
	using EdgeProperty = boost::property<boost::edge_weight_t, int, boost::property<boost::edge_index_t, char>>;  
	using UndirectedGraph  
		= boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS, boost::no_property, EdgeProperty>;  
  
	UndirectedGraph graph;  
  
	const auto a = boost::add_vertex(graph);  
	const auto b = boost::add_vertex(graph);  
	const auto c = boost::add_vertex(graph);  
	const auto d = boost::add_vertex(graph);  
  
  
	boost::add_edge(a, b, 1, graph);  
	boost::add_edge(a, c, 0, graph);  
	boost::add_edge(b, d, 3, graph);  
	boost::add_edge(c, d, 2, graph);  
  
	std::vector<typename boost::graph_traits<UndirectedGraph>::vertex_descriptor> mate(boost::num_vertices(graph));  
	boost::maximum_weighted_matching(graph, &mate[0]);  
  
	for (auto [vi, vend] = boost::vertices(graph); vi != vend; ++vi)  
	{  
		if (mate[*vi] != boost::graph_traits<UndirectedGraph>::null_vertex() && *vi < mate[*vi])  
		{  
			std::cout << *vi << " " << mate[*vi] << std::endl;  
		}  
	}  
}  
```  
  
I got on my system only one pair printed, but expect two pairs for 4 vertices. Is zero edge cost allowed? If I add 1 for every edge cost:  
```  
boost::add_edge(a, b, 2, graph);  
boost::add_edge(a, c, 1, graph);  
boost::add_edge(b, d, 4, graph);  
boost::add_edge(c, d, 3, graph);  
```  
than I got two pairs printed as expected. It could be a-c and b-d or a-b and c-d, because we have two equal matchings.

---

## Comment 1

> Username: Becheler  
> Created at: 2025-08-24 19:45:22 UTC  
> Url: https://github.com/boostorg/graph/issues/436#issuecomment-3218331496  

Zero edge cost is allowed. I think what you’re seeing is expected: `maximum_weighted_matching` maximizes total weight only,  
- It looks for total weight maximum, not maximum cardinality (number of edges)  
- If multiple matchings achieve the same total weight, any one of them is a valid answer; the algorithm does not try to maximize cardinality or ensure uniqueness  
  
In your 4-vertex example, the best one-edge matching {b,d} has weight 3, and the best two-edge matchings {a,b}+{c,d} and {a,c}+{b,d} also total 3. Since they tie, the algorithm is free to return any maximum-weight matching — including the single pair {b,d}.  
  
When you add +1 to every edge weight, the best two-edge matching becomes 2+3=5, which beats any one-edge matching (max 4). That’s why you then see two pairs.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2025-08-25 05:37:47 UTC  
> Url: https://github.com/boostorg/graph/issues/436#issuecomment-3218898568  

Becheler is spot on, so I'm going to close this issue. Please re-open if we have misunderstood.
