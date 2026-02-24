# #686 - help on find in tuple [Closed]

> Username: ppacory  
> Created at: 2017-01-17 10:56:14 UTC  
> Updated at: 2017-01-17 14:28:40 UTC  
> Closed at: 2017-01-17 14:28:35 UTC  
> Url: https://github.com/boostorg/compute/issues/686  

how to compute::find in a tuple ?  
  
I try this code but the find doesn't work ...  
  
``  
#include <cstdint>  
#include <vector>  
#include <algorithm>  
#include <boost/compute.hpp>  
  
namespace compute = boost::compute;  
  
int main()  
{  
  // get default device and setup context  
  compute::device device = compute::system::default_device();  
  compute::context context1(device);  
  
  int numberOfNodes=200000;  
  std::vector< compute::command_queue* > queues(1); // a vector of compute::command_queue  
  std::vector< compute::vector<boost::tuple<uint64_t, uint64_t>>* > nodes(numberOfNodes); // a vector of <compute::vector<boost::tuple>>  
  
  queues[0] = new compute::command_queue(context1, device); // create a queue job  
   
  for (int i=0;i<numberOfNodes;i++)  
  {  
	nodes[i] = new compute::vector<boost::tuple<uint64_t, uint64_t>> (5000,context1);    
    compute::fill(nodes[i]->begin(), nodes[i]->end(), boost::make_tuple(1, i), *queues[0]);   
  
    // find in tuple  
    compute::vector<uint64_t>::iterator iter = boost::get<0>(  
        compute::find(   
          compute::make_zip_iterator(  
            nodes[i]->begin()  
          ),    
          compute::make_zip_iterator(  
            nodes[i]->end()  
          ),    
          555666,   
          *queues[0]  
        ).get_iterator_tuple()  
    );  
      
    int index = std::distance(nodes[i]->begin(), iter);  
    std::cout << "value at index: " << index << std::endl;  
  
  }   
    
  return 0;  
  
}  
``
