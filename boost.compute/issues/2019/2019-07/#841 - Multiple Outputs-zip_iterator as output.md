# #841 - Multiple Outputs/zip_iterator as output [Open]

> Username: rgreenblatt  
> Created at: 2019-07-16 16:08:41 UTC  
> Updated at: 2019-07-16 16:08:41 UTC  
> Url: https://github.com/boostorg/compute/issues/841  

Is there any way to have multiple outputs in general? `zip_iterators` work for multiple inputs, but don't allow for multiple outputs. I would ideally expect the following to work, but it doesn't:  
```  
#include <boost/compute.hpp>  
#include <iostream>  
#include <vector>  
  
namespace bc = boost::compute;  
  
int main(int argc, char *argv[]) {  
  bc::vector<int> data(bc::counting_iterator<int>(0),  
                       bc::counting_iterator<int>(10));  
  bc::vector<int> output_1(data.size());  
  bc::vector<int> output_2(data.size());  
  
  // BOOST_COMPUTE_FUNCTION can't handle templated return types  
  auto duplicate = bc::detail::make_function_impl<boost::tuple<int, int>(int)>(  
      "duplicate", BOOST_COMPUTE_STRINGIZE_SOURCE((int value)),  
      BOOST_COMPUTE_STRINGIZE_SOURCE({  
        return (boost_tuple_int_int_t){value, value};  
      }));  
  
  bc::transform(data.begin(), data.end(),  
                bc::make_zip_iterator(  
                    boost::make_tuple(output_1.begin(), output_2.begin())),  
                duplicate);  
  
  std::vector<int> output_1_cpu(data.size());  
  std::vector<int> output_2_cpu(data.size());  
  bc::copy(output_1.begin(), output_1.end(), output_1_cpu.begin());  
  bc::copy(output_2.begin(), output_2.end(), output_2_cpu.begin());  
  for (size_t i = 0; i < output_1_cpu.size(); ++i) {  
    std::cout << "1: " << output_1_cpu[i] << " 2: " << output_2_cpu[i] << "\n";  
  }  
  
  return 0;  
}  
```  
It fails because the generated kernel doesn't assign to output_1 and output_2. Instead, a new struct/tuple is constructed and assigned to.
