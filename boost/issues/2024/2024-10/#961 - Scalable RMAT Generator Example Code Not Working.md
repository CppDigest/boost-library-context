# #961 - Scalable RMAT Generator Example Code Not Working [Open]

> Username: kaanolgu  
> Created at: 2024-10-06 11:31:16 UTC  
> Updated at: 2024-10-06 11:31:16 UTC  
> Url: https://github.com/boostorg/boost/issues/961  

Hi,   
I am trying to follow the instructions here https://live.boost.org/doc/libs/1_86_0/libs/graph_parallel/doc/html/scalable_rmat_generator.html  
  
but the issue is that example code does not work :  
```  
#include <boost/graph/distributed/mpi_process_group.hpp>  
#include <boost/graph/compressed_sparse_row_graph.hpp>  
#include <boost/graph/rmat_graph_generator.hpp>  
#include <boost/random/linear_congruential.hpp>  
  
using boost::graph::distributed::mpi_process_group;  
  
typedef compressed_sparse_row_graph<directedS, no_property, no_property, no_property,  
                                    distributedS<mpi_process_group> > Graph;  
typedef boost::scalable_rmat_iterator<boost::minstd_rand, Graph> RMATGen;  
  
int main()  
{  
  boost::minstd_rand gen;  
  mpi_process_group pg;  
  
  int N = 100;  
  
  boost::parallel::variant_distribution<ProcessGroup> distrib  
    = boost::parallel::block(pg, N);  
  
  // Create graph with 100 nodes and 400 edges  
  Graph g(RMATGen(pg, distrib, gen, N, 400, 0.57, 0.19, 0.19, 0.05),  
          RMATGen(), N, pg, distrib);  
  return 0;  
}  
```  
  
First it complained that `#include <boost::graph::use_mpi.hpp>` was missing and then bunch of variables needed namespace like `boost::directedS` but then `ProcessGroup` complained that it does not exist and this function template requires 4 arguments but received 2 error `typedef boost::scalable_rmat_iterator<boost::minstd_rand, Graph>`   
  
I would like to get help to make this code including all missing parts to get it working.   
Thank you in advance
