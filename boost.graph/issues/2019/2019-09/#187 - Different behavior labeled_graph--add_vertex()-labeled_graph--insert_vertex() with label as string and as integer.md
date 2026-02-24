# #187 - Different behavior labeled_graph::add_vertex()/labeled_graph::insert_vertex() with label as string and as integer. [Open]

> Username: AlekseyMuraviev  
> Created at: 2019-09-30 12:46:47 UTC  
> Updated at: 2019-09-30 12:46:47 UTC  
> Url: https://github.com/boostorg/graph/issues/187  

If I try to use a string as a label, the result is expected. If an integer, then the result is different than expected.  
  
```c++  
#include <iostream>  
#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/labeled_graph.hpp>  
  
int main()  
{  
		using namespace std;  
		cout << "add_vertex():\n";  
		{  
			using LabeledGraph = boost::labeled_graph<boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS>, string>;  
			LabeledGraph g;  
			const auto vd1 = g.add_vertex("123");  
			const auto vd2 = g.add_vertex("123");  
			cout << " string:\n";  
			cout << "  " << boolalpha << (vd1 == vd2) << endl;  
		}  
		{  
			using LabeledGraph = boost::labeled_graph<boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS>, size_t>;  
			LabeledGraph g;  
			const auto vd1 = g.add_vertex(123);  
			const auto vd2 = g.add_vertex(123);  
			cout << " size_t:\n";  
			cout << "  " << boolalpha << (vd1 == vd2) << endl;  
		}  
		cout << "insert_vertex():\n";  
		{  
			using LabeledGraph = boost::labeled_graph<boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS>, string>;  
			LabeledGraph g;  
			cout << " string:\n";  
			cout << "  " << boolalpha << g.insert_vertex("123").second << endl;  
			cout << "  " << boolalpha << g.insert_vertex("123").second << endl;  
		}  
		{  
			using LabeledGraph = boost::labeled_graph<boost::adjacency_list<boost::vecS, boost::vecS, boost::undirectedS>, size_t>;  
			LabeledGraph g;  
			cout << " size_t:\n";  
			cout << "  " << boolalpha << g.insert_vertex(123).second << endl;  
			cout << "  " << boolalpha << g.insert_vertex(123).second << endl;  
		}  
}  
```  
  
If I compile this code using the following command line, I get this result.  
`g++ prog.cc -Wall -Wextra -I/opt/wandbox/boost-1.71.0/gcc-head/include -std=gnu++2a`  
  
```  
add_vertex():  
 string:  
  true  
 size_t:  
  false  
insert_vertex():  
 string:  
  true  
  false  
 size_t:  
  true  
  true  
```
