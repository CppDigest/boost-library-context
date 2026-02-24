# #223 - maximum_weighted_matching never terminates for some edge orders [Open]

> Username: pascalhpi  
> Created at: 2020-06-10 18:38:13 UTC  
> Updated at: 2025-02-15 10:19:31 UTC  
> Url: https://github.com/boostorg/graph/issues/223  

For maximum_weighted_matching the order in which edges are added to the graph makes a difference. For one order it works perfectly, but for the other the algorithm never terminates. Here is a modified version of the example to show the problem:  
```c++  
//=======================================================================  
// Copyright (c) 2018 Yi Ji  
//  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
//  
//=======================================================================  
  
#include <iostream>  
#include <vector>  
#include <string>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/maximum_weighted_matching.hpp>  
  
using namespace boost;  
  
typedef property< edge_weight_t, int, property< edge_index_t, int > >  
    EdgeProperty;  
typedef adjacency_list< vecS, vecS, undirectedS, no_property, EdgeProperty >  
    my_graph;  
  
int main(int argc, const char* argv[])  
{  
    graph_traits< my_graph >::vertex_iterator vi, vi_end;  
    const int n_vertices = 10;  
    my_graph g(n_vertices);  
  
    // vertices can be refered by integers because my_graph use vector to store  
    // them  
  
    // NOT WORKING  
    add_edge(8, 2, EdgeProperty(10000-1002), g);  
    add_edge(1, 4, EdgeProperty(10000-502), g);  
    add_edge(9, 3, EdgeProperty(10000-1007), g);  
    add_edge(2, 6, EdgeProperty(10000-1), g);  
    add_edge(9, 7, EdgeProperty(10000-1002), g);  
    add_edge(0, 5, EdgeProperty(10000-1504), g);  
    add_edge(9, 4, EdgeProperty(10000-1006), g);  
    add_edge(9, 5, EdgeProperty(10000-1005), g);  
    add_edge(9, 6, EdgeProperty(10000-1004), g);  
    add_edge(0, 6, EdgeProperty(10000-1505), g);  
    add_edge(1, 7, EdgeProperty(10000-506), g);  
    add_edge(0, 3, EdgeProperty(10000-1502), g);  
    add_edge(0, 4, EdgeProperty(10000-1503), g);  
    add_edge(2, 4, EdgeProperty(10000-3), g);  
    add_edge(1, 6, EdgeProperty(10000-504), g);  
    add_edge(8, 5, EdgeProperty(10000-1004), g);  
    add_edge(1, 3, EdgeProperty(10000-501), g);  
    add_edge(4, 7, EdgeProperty(10000-4), g);  
    add_edge(3, 7, EdgeProperty(10000-5), g);  
    add_edge(3, 6, EdgeProperty(10000-3), g);  
    add_edge(8, 3, EdgeProperty(10000-1006), g);  
    add_edge(1, 5, EdgeProperty(10000-503), g);  
    add_edge(8, 6, EdgeProperty(10000-1003), g);  
    add_edge(2, 7, EdgeProperty(10000-1), g);  
    add_edge(0, 8, EdgeProperty(10000-2508), g);  
    add_edge(0, 9, EdgeProperty(10000-2509), g);  
    add_edge(5, 7, EdgeProperty(10000-3), g);  
    add_edge(1, 2, EdgeProperty(10000-505), g);  
    add_edge(2, 5, EdgeProperty(10000-2), g);  
    add_edge(8, 4, EdgeProperty(10000-1005), g);  
  
    // WORKING - same edges, different order  
    // add_edge(0, 3, EdgeProperty(10000-1502), g);  
    // add_edge(0, 4, EdgeProperty(10000-1503), g);  
    // add_edge(0, 5, EdgeProperty(10000-1504), g);  
    // add_edge(0, 6, EdgeProperty(10000-1505), g);  
    // add_edge(0, 8, EdgeProperty(10000-2508), g);  
    // add_edge(0, 9, EdgeProperty(10000-2509), g);  
    // add_edge(1, 2, EdgeProperty(10000-505), g);  
    // add_edge(1, 3, EdgeProperty(10000-501), g);  
    // add_edge(1, 4, EdgeProperty(10000-502), g);  
    // add_edge(1, 5, EdgeProperty(10000-503), g);  
    // add_edge(1, 6, EdgeProperty(10000-504), g);  
    // add_edge(1, 7, EdgeProperty(10000-506), g);  
    // add_edge(2, 4, EdgeProperty(10000-3), g);  
    // add_edge(2, 5, EdgeProperty(10000-2), g);  
    // add_edge(2, 6, EdgeProperty(10000-1), g);  
    // add_edge(2, 7, EdgeProperty(10000-1), g);  
    // add_edge(3, 6, EdgeProperty(10000-3), g);  
    // add_edge(3, 7, EdgeProperty(10000-5), g);  
    // add_edge(4, 7, EdgeProperty(10000-4), g);  
    // add_edge(5, 7, EdgeProperty(10000-3), g);  
    // add_edge(8, 2, EdgeProperty(10000-1002), g);  
    // add_edge(8, 3, EdgeProperty(10000-1006), g);  
    // add_edge(8, 4, EdgeProperty(10000-1005), g);  
    // add_edge(8, 5, EdgeProperty(10000-1004), g);  
    // add_edge(8, 6, EdgeProperty(10000-1003), g);  
    // add_edge(9, 3, EdgeProperty(10000-1007), g);  
    // add_edge(9, 4, EdgeProperty(10000-1006), g);  
    // add_edge(9, 5, EdgeProperty(10000-1005), g);  
    // add_edge(9, 6, EdgeProperty(10000-1004), g);  
    // add_edge(9, 7, EdgeProperty(10000-1002), g);  
  
    std::vector< graph_traits< my_graph >::vertex_descriptor > mate1(  
        n_vertices),  
        mate2(n_vertices);  
    maximum_weighted_matching(g, &mate1[0]);  
  
    std::cout << "Found a weighted matching:" << std::endl;  
    std::cout << "Matching size is " << matching_size(g, &mate1[0])  
              << ", total weight is " << matching_weight_sum(g, &mate1[0])  
              << std::endl;  
    std::cout << std::endl;  
  
    std::cout << "The matching is:" << std::endl;  
    for (boost::tie(vi, vi_end) = vertices(g); vi != vi_end; ++vi)  
        if (mate1[*vi] != graph_traits< my_graph >::null_vertex()  
            && *vi < mate1[*vi])  
            std::cout << "{" << *vi << ", " << mate1[*vi] << "}" << std::endl;  
    std::cout << std::endl;  
}  
```

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:09:03 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-1181354652  

Can you make a pull request with a new unit test using this code?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2022-07-12 06:42:10 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-1181377499  

Tagging @yi-ji .

---

## Comment 3

> Username: tomaThomas  
> Created at: 2023-12-04 12:50:25 UTC  
> Updated at: 2023-12-04 13:06:53 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-1838569598  

I got the same issue with non integer edge weights  
The issue is reproducible with the existing unit test, just add the graphs to `weighted_matching.dat`:  
Working:  
```5989 10 30 0 3 8498 0 4 8497 0 5 8496 0 6 8495 0 8 7492 0 9 7491 1 2 9495 1 3 9499 1 4 9498 1 5 9497 1 6 9496 1 7 9494 2 4 9997 2 5 9998 2 6 9999 2 7 9999 3 6 9997 3 7 9995 4 7 9996 5 7 9997 8 2 8998 8 3 8994 8 4 8995 8 5 8996 8 6 8997 9 3 8993 9 4 8994 9 5 8995 9 6 8996 9 7 8998```  
Not working:  
```45989 10 30 8 2 8998 1 4 9498 9 3 8993 2 6 9999 9 7 8998 0 5 8496 9 4 8994 9 5 8995 9 6 8996 0 6 8495 1 7 9494 0 3 8498 0 4 8497 2 4 9997 1 6 9496 8 5 8996 1 3 9499 4 7 9996 3 7 9995 3 6 9997 8 3 8994 1 5 9497 8 6 8997 2 7 9999 0 8 7492 0 9 7491 5 7 9997 1 2 9495 2 5 9998 8 4 8995```  
  
This is useful for a workaround: https://stackoverflow.com/questions/65327854/issue-using-cpp-boost-maximum-weighted-matching-algorithm-with-floating-point-ed

---

## Comment 4

> Username: yi-ji  
> Created at: 2024-05-09 22:03:28 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-2103485965  

I looked into it a few months ago and I was half way through a fix on the corner cases, but didn't have time to finish it. I will try my best to find personal time to continue.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2024-05-10 06:21:51 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-2103946238  

> I looked into it a few months ago and I was half way through a fix on the corner cases, but didn't have time to finish it. I will try my best to find personal time to continue.  
  
Thank you. I know many of us don't have much spare time, so I appreciate whatever you can afford to do.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2025-02-15 10:19:30 UTC  
> Url: https://github.com/boostorg/graph/issues/223#issuecomment-2660856562  

@pascalhpi , @tomaThomas please confirm that this bug is fixed.
