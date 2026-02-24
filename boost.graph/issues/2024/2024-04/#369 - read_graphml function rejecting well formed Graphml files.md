# #369 - read_graphml function rejecting well formed Graphml files [Open]

> Username: bhayat-quantinuum  
> Created at: 2024-04-04 08:52:08 UTC  
> Updated at: 2025-12-29 01:11:09 UTC  
> Url: https://github.com/boostorg/graph/issues/369  

Here is a MWE of my code:  
  
```  
#include <iostream>  
#include <fstream>  
#include <boost/graph/graphml.hpp>  
#include <boost/property_map/dynamic_property_map.hpp>  
  
int main(int, char**){  
  
    std::ifstream graph_file;  
    graph_file.open("more_exampleHoles_8.txt", std::ifstream::in);  
  
    Graph g;  
    boost::dynamic_properties dp;  
    boost::read_graphml(graph_file, g, dp);  
}  
```  
  
The file `more_exampleHoles_8.txt` is attached below. I receive the following error:  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::property_not_found>'  
  what():  Property not found: betti.  
Aborted (core dumped)  
```  
  
I think this is a bug? If not, any help appreciated.  
  
[more_exampleHoles_8.txt](https://github.com/boostorg/graph/files/14863835/more_exampleHoles_8.txt)

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-04-04 23:23:28 UTC  
> Url: https://github.com/boostorg/graph/issues/369#issuecomment-2038435201  

Do you have something else that reads GraphML to compare against? I mean, how did you verify that it's well formed?

---

## Comment 2

> Username: andreacassioli  
> Created at: 2025-12-28 21:00:46 UTC  
> Url: https://github.com/boostorg/graph/issues/369#issuecomment-3695045317  

If it can help, I have tried with python NetworkX package and the example seems legit, no error. However boost does indeed raises an exception.

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2025-12-29 01:11:09 UTC  
> Url: https://github.com/boostorg/graph/issues/369#issuecomment-3695212267  

Thanks for the confirmation.
