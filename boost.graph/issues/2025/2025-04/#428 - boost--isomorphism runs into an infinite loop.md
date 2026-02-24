# #428 - boost::isomorphism runs into an infinite loop [Closed]

> Username: chrWeberAFDE  
> Created at: 2025-04-16 08:49:04 UTC  
> Updated at: 2025-05-12 08:55:21 UTC  
> Closed at: 2025-05-12 08:55:21 UTC  
> Url: https://github.com/boostorg/graph/issues/428  

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
[graph0.dot.txt](https://github.com/user-attachments/files/19773816/graph0.dot.txt)  
[graph2.dot.txt](https://github.com/user-attachments/files/19773817/graph2.dot.txt)  
  
*  boost::isomorphism will never finish  
* I would expect 'true' as a result for the function  
##  Version and compiler infos  
  
* I encountered the issue while upgrading boost's version from 1.81.0 to 1.86.0. It worked fine with 1.81.0 and does not work with 1.86.0.  
* compiler version is  ````Microsoft (R) C/C++ Optimizing Compiler Version 19.38.33133 for x86````

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2025-04-30 10:27:01 UTC  
> Url: https://github.com/boostorg/graph/issues/428#issuecomment-2841522517  

Thanks for documenting this, I'll prioritise it.   
We did make some substantial changes to the isomorphism implementation, so I'll ask @jan-grimo if he has time to investigate.

---

## Comment 2

> Username: jan-grimo  
> Created at: 2025-05-01 11:05:46 UTC  
> Url: https://github.com/boostorg/graph/issues/428#issuecomment-2844652524  

Thanks for the detailed bug report! A quick bisect reveals that 8f3139bed4da6510e523b3f1c6f981a47d4b7d54 as part of my PR #349 is to blame. I should have this fixed quick, it has to be a dangling reference or so.

---

## Comment 3

> Username: chrWeberAFDE  
> Created at: 2025-05-02 07:08:16 UTC  
> Url: https://github.com/boostorg/graph/issues/428#issuecomment-2846523170  

Thanks for fixing. Meanwhile I  also found a good workaround by providing vertex invariance maps which works, because in my original problem ,I have properties assigned to the nodes .
