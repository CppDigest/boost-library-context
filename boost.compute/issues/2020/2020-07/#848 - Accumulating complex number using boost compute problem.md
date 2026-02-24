# #848 - Accumulating complex number using boost compute problem [Open]

> Username: ariansharifi  
> Created at: 2020-07-02 17:04:48 UTC  
> Updated at: 2020-07-02 17:04:48 UTC  
> Url: https://github.com/boostorg/compute/issues/848  

this below code face with a compile error, but it is reasonable to accumulate complex number (because I can accumulate complex number using boost compute with boost::transfare_reduce so this should be also reasonable to works with complex numbers)  
```  
#include <boost/compute/core.hpp>  
#include <boost/compute/platform.hpp>  
#include <boost/compute/algorithm.hpp>  
#include <boost/compute/types/complex.hpp>  
#include <complex>  
  
namespace compute = boost::compute;  
  
int main()  
{  
compute::vector<std::complex<double_t>> v;  
compute::accumulate(v.begin(),v.end(),std::complex<double_t>(0,0));  
}  
```  
and I face with below compile error  
```  
In file included from ~/boost/compute/memory_object.hpp:16:0,  
                from ~/boost/compute/buffer.hpp:17,  
                from ~/boost/compute/core.hpp:18,  
                from ~/main.cpp:1:  
~/boost/compute/kernel.hpp: In instantiation of ‘struct boost::compute::detail::set_kernel_arg<std::complex<double> >’:  
~/boost/compute/kernel.hpp:251:17:   required from ‘void boost::compute::kernel::set_arg(size_t, const T&) [with T = std::complex<double>; size_t = long unsigned int]’  
~/boost/compute/algorithm/detail/serial_accumulate.hpp:46:5:   required from ‘void boost::compute::detail::serial_accumulate(InputIterator, InputIterator, OutputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; OutputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:46:30:   required from ‘T boost::compute::detail::generic_accumulate(InputIterator, InputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:118:34:   required from ‘T boost::compute::detail::dispatch_accumulate(InputIterator, InputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:182:39:   required from ‘T boost::compute::accumulate(InputIterator, InputIterator, T, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>]’  
~/main.cpp:22:70:   required from here  
~/boost/compute/kernel.hpp:398:5: error: no type named ‘type’ in ‘struct boost::enable_if<boost::compute::is_fundamental<std::complex<double> >, void>’  
    operator()(kernel &kernel_, size_t index, const T &value)  
    ^~~~~~~~  
In file included from ~/boost/compute/memory_object.hpp:16:0,  
                from ~/boost/compute/buffer.hpp:17,  
                from ~/boost/compute/core.hpp:18,  
                from ~/main.cpp:1:  
~/boost/compute/kernel.hpp: In instantiation of ‘void boost::compute::kernel::set_arg(size_t, const T&) [with T = std::complex<double>; size_t = long unsigned int]’:  
~/boost/compute/algorithm/detail/serial_accumulate.hpp:46:5:   required from ‘void boost::compute::detail::serial_accumulate(InputIterator, InputIterator, OutputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; OutputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:46:30:   required from ‘T boost::compute::detail::generic_accumulate(InputIterator, InputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:118:34:   required from ‘T boost::compute::detail::dispatch_accumulate(InputIterator, InputIterator, T, BinaryFunction, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>; BinaryFunction = boost::compute::plus<std::complex<double> >]’  
~/boost/compute/algorithm/accumulate.hpp:182:39:   required from ‘T boost::compute::accumulate(InputIterator, InputIterator, T, boost::compute::command_queue&) [with InputIterator = boost::compute::buffer_iterator<std::complex<double> >; T = std::complex<double>]’  
~/main.cpp:22:70:   required from here  
~/boost/compute/kernel.hpp:251:36: error: no match for call to ‘(boost::compute::detail::set_kernel_arg<std::complex<double> >) (boost::compute::kernel&, size_t&, const std::complex<double>&)’  
        detail::set_kernel_arg<T>()(*this, index, value);  
        ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
```
