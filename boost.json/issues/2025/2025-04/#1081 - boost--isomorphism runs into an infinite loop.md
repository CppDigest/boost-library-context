# #1081 - boost::isomorphism runs into an infinite loop [Closed]

> Username: chrWeberAFDE  
> Created at: 2025-04-16 08:35:43 UTC  
> Updated at: 2025-04-16 09:37:00 UTC  
> Closed at: 2025-04-16 09:37:00 UTC  
> Url: https://github.com/boostorg/json/issues/1081  

## How to reproduce  
  
* compile this code:  
  
  
```  
   struct VertexProps  
   {  
        std::string node_id; // will store "0", "1", ..., "74"  
   };  
  
using Graph = boost::adjacency_list<boost::vecS, boost::vecS, boost::bidirectionalS, VertexProps>;  
void  loadGraphFromDOT(const std::string& filename, Graph& g)  
{  
   std::ifstream in(filename);  
   if (!in)  
   {  
        throw std::runtime_error("Error: Cannot open file ");  
          
   }  
   auto node_id_map = boost::get(&VertexProps::node_id, g);  
   boost::dynamic_properties dp;  
   dp.property("node_id", node_id_map);  
  
   if (!boost::read_graphviz(in, g, dp))  
   {  
        throw std::runtime_error("Error: Failed to read DOT ");          
   }  
}  
  
  
  
TEST(GraphTest, isomerphismTest)  
{  
   using Graph = boost::adjacency_list<boost::vecS, boost::vecS, boost::bidirectionalS, VertexProps>;  
   Graph g0;  
   Graph g1;  
   loadGraphFromDOT("graph0.dot", g0);  
   loadGraphFromDOT("graph2.dot", g1);  
  
   const bool iso = boost::isomorphism(g0, g1);  
   EXPECT_TRUE(iso);  
}  
```  
  
* run the code with the given dot files  (adjust the file ending)  
[graph0.dot.txt](https://github.com/user-attachments/files/19773405/graph0.dot.txt)  
[graph2.dot.txt](https://github.com/user-attachments/files/19773404/graph2.dot.txt)  
  
*  boost::isomorphism will never finish  
* I would expect 'true' as a result for the function  
##  Version and compiler infos  
  
* I encountered the issue while upgrading boost's version from 1.81.0 to 1.86.0. It worked fine with 1.81.0 and does not work with 1.86.0.  
* compiler version is  ````Microsoft (R) C/C++ Optimizing Compiler Version 19.38.33133 for x86````

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-04-16 08:41:31 UTC  
> Url: https://github.com/boostorg/json/issues/1081#issuecomment-2808836676  

This looks like it should have been filed against Boost.Graph.

---

## Comment 2

> Username: chrWeberAFDE  
> Created at: 2025-04-16 08:50:03 UTC  
> Url: https://github.com/boostorg/json/issues/1081#issuecomment-2808858401  

oh, my mistake. I have put it there. Feel free to remove this issue here.
