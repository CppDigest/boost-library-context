# #214 - [Question/Issue] Add named vertex does not work proper [Open]

> Username: DwarKapex  
> Created at: 2020-04-07 16:31:46 UTC  
> Updated at: 2020-04-11 00:43:26 UTC  
> Url: https://github.com/boostorg/graph/issues/214  

Hi guys!  
  
   I'm studying BGL now and I found a [tutorial](https://github.com/richelbilderbeek/BoostGraphTutorial/blob/master/boost_graph_tutorial.pdf) for it. Everything worked fine from it until I reached  function to add_named_vertex. Here is a piece of code I have, that does not work as I (and tutorial) expect:  
```#include <boost/graph/adjacency_list.hpp>  
#include <boost/graph/graphviz.hpp>  
#include <type_traits>  
#include <iostream>  
#include <sstream>  
#include <string>  
  
boost::adjacency_list<  
    boost::vecS,  
    boost::vecS,  
    boost::directedS,  
    boost::property<boost::vertex_name_t, std::string>  
>  
create_empty_graph() { return {}; }  
  
template<typename graph, typename name_type>  
typename boost::graph_traits<graph>::vertex_descriptor  
add_named_vertex(const name_type& vertex_name, graph& g) noexcept {  
    const auto vd = boost::add_vertex(g);  
    auto vertex_name_map = get(boost::vertex_name, g);  
    put(vertex_name_map, vd, vertex_name);  
    return vd;  
}  
  
int main()  
{  
    auto g = create_empty_graph();  
    const auto va = add_named_vertex("One", g);  
    const auto vb = add_named_vertex("Two", g);  
    boost::add_edge(va,vb, g);  
  
    std::stringstream f;  
    boost::write_graphviz(f, g);  
    std::cout << f.str() << std::endl;  
      
    return 0;  
}  
```  
I expect:   
```   
digraph G {  
0[label=One];  
1[label=Two];  
0->1;  
}  
```  
But here is what I got:  
```  
digraph G {  
0;  
1;  
0->1;  
}  
```  
As you can see, there are no labels in the output of this code. Could you please tell me, what am I missing? Is it expected behavior?  
  
Thanks you very much in advance for your great work!

---

## Comment 1

> Username: jeffythedragonslayer  
> Created at: 2020-04-10 23:34:27 UTC  
> Url: https://github.com/boostorg/graph/issues/214#issuecomment-612265800  

I'm not sure, I've used a std::map<vertex_t, std::string> in the past as a workaround.

---

## Comment 2

> Username: DwarKapex  
> Created at: 2020-04-11 00:22:01 UTC  
> Url: https://github.com/boostorg/graph/issues/214#issuecomment-612277782  

@jeffythedragonslayer can you provide a code snippet for that? =) And if you know a good tutorial for boost graph library, I would be more then appreciated for it!

---

## Comment 3

> Username: jeffythedragonslayer  
> Created at: 2020-04-11 00:39:23 UTC  
> Updated at: 2020-04-11 00:43:26 UTC  
> Url: https://github.com/boostorg/graph/issues/214#issuecomment-612281402  

https://github.com/jeffythedragonslayer/lipton-tarjan  
checkout e92e73ac2e6c295858f59372ecd35683e3d78297 it's the last commit before I deleted it :o  
See Vert2UintMap.{h, cpp}  
Replace the uints with strings and there you go :)  
  
I'm actually working on a ppt presentation/talk on understanding the boost graph library for CppMaryland at the moment as everything I found at the time of starting that project was unrelentingly dense.  (Here's an example: https://github.com/richelbilderbeek/BoostGraphTutorial/blob/master/boost_graph_tutorial.pdf)
