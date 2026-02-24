# #247 - boyer_myrvold_planarity_test: Apparent quadratic time complexity for prism graphs [Open]

> Username: mnbvmar  
> Created at: 2021-04-09 15:41:33 UTC  
> Updated at: 2022-03-06 12:15:57 UTC  
> Url: https://github.com/boostorg/graph/issues/247  

The Boyer-Myrvold planarity test for [prism graphs](https://en.wikipedia.org/wiki/Prism_graph) appears to run in quadratic time complexity:  
  
``` c++  
#include <iostream>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/boyer_myrvold_planar_test.hpp>  
#include <boost/timer/timer.hpp>  
  
using namespace std;  
using namespace boost;  
  
// Follows libs/graph/example/simple_planarity_test.cpp.  
using ugraph = adjacency_list<vecS, vecS, undirectedS,  
                              property<vertex_index_t, int>>;  
  
ugraph construct_wheel_graph(size_t k) {  
	const size_t num_vertices = 2 * k;  
	ugraph G(num_vertices);  
  
	auto add_cycle = [&](int from, int to) {  
		for (int i = from; i < to; ++i) {  
			add_edge(i, i + 1, G);  
		}  
		add_edge(to, from, G);  
	};  
  
	// Add inner and outer cycle.  
	add_cycle(0, k - 1);  
	add_cycle(k, 2 * k - 1);  
  
	// Add spokes.  
	for (int i = 0; i < k; ++i) {  
		add_edge(i, k + i, G);  
	}  
  
	return G;  
}  
  
// Testing harness.  
void run_test() {  
	for (int k = 16; k < 35 * 1000; k *= 2) {  
		ugraph G = construct_wheel_graph(k);  
		cout << "V=" << num_vertices(G) << "\t";  
		cout << "E=" << num_edges(G) << "\t" << flush;  
  
		timer::auto_cpu_timer tm(3, "%w seconds\n");  
		if (boyer_myrvold_planarity_test(G)) {  
			cout << "planar\t";  
		} else {  
			cout << "not planar\t";  
		}  
	}  
}  
  
int main(int argc, char** argv) {  
	run_test();  
}  
```  
  
Compile with `-O2 -DNDEBUG -lboost_timer`. Possible output:  
```  
V=32    E=48    planar  0.000 seconds  
V=64    E=96    planar  0.000 seconds  
V=128   E=192   planar  0.001 seconds  
V=256   E=384   planar  0.001 seconds  
V=512   E=768   planar  0.003 seconds  
V=1024  E=1536  planar  0.008 seconds  
V=2048  E=3072  planar  0.028 seconds  
V=4096  E=6144  planar  0.096 seconds  
V=8192  E=12288 planar  0.366 seconds  
V=16384 E=24576 planar  1.536 seconds  
V=32768 E=49152 planar  5.606 seconds  
V=65536 E=98304 planar  23.151 seconds  
```  
  
  
The algorithm speeds up significantly (apparently, to linear time complexity) if the edges are first shuffled, or even if the spokes are added to the graph before the cycles.  
  
Tested on Linux; the issue occurs both in 1.75.0 and in the repo version. I'm using GCC 10.2.0.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2022-03-06 12:15:57 UTC  
> Url: https://github.com/boostorg/graph/issues/247#issuecomment-1059952112  

Oh, that is weird. Well, you've found the answer -- add in some random shuffling! ;)  
  
Of course, other solutions are welcome too.
