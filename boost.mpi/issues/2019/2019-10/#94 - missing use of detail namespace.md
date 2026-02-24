# #94 - missing use of detail namespace [Closed]

> Username: GeorgeLykoudis  
> Created at: 2019-10-17 13:49:38 UTC  
> Updated at: 2019-10-22 14:17:25 UTC  
> Closed at: 2019-10-22 14:17:25 UTC  
> Url: https://github.com/boostorg/mpi/issues/94  

I am using boost **1.71.0**. In mpi/collectives/scatterv.hpp no **scatterv_impl** function is implemented, out of scope of "detail" namespace.  
  
`boost/include/boost/mpi/collectives/scatterv.hpp:104:16: error: ‘scatterv_impl’ was not declared in this scope; did you mean ‘boost::mpi::detail::scatterv_impl’?`

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-10-20 19:01:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544281461  

Hi @GiwrgosLykoudis , do you have a test case ?

---

## Comment 2

> Username: GeorgeLykoudis  
> Created at: 2019-10-21 08:01:40 UTC  
> Updated at: 2019-10-21 09:43:14 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544397145  

I think the problem is in the first implementation of boost::mpi::scatterv out of scope of detail namespace, where scatterv_impl is called without **detail** specification.    
  
A simple example is the following. Eigen is a cpp header only library for linear algebra.  
  
```  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/cartesian_communicator.hpp>  
#include "Eigen/Dense"  
  
int main(int argc, char **argv)  
{  
        static constexpr int size = 3;  
  
        std::array<Eigen::MatrixXd, size> matrix_in;    
        std::array<Eigen::MatrixXd, size> matrix_out;    
  
        boost::mpi::environment    env(argc, argv, true);  
	boost::mpi::communicator world;         
  
        std::array<boost::mpi::cartesian_dimension, size> procs;  
  
	for(std::size_t i=0; i<size; i++) {  
		procs[i] = {2, true};  
        matrix_in[i].resize(4,4);  
        matrix_in[i].setRandom();  
          
        matrix_out[i].resize(4,4);  
        matrix_out[i].setZero();  
	}  
	boost::mpi::cartesian_communicator grid_comm(world, boost::mpi::cartesian_topology(procs), true);   
	  
	std::vector<boost::mpi::cartesian_communicator> layer_comm; layer_comm.reserve(size);  
	std::vector<boost::mpi::cartesian_communicator> fiber_comm; fiber_comm.reserve(size);  
  
        std::vector<int>      layer_dims(size-1);  
        std::array<int, size> _temp;  
	for (std::size_t i=0; i<size; i++)  
	{  
		std::fill(_temp.begin(), _temp.end(), 1);  
		_temp[i] = 0;  
  
		int pos = 0;  
		for(std::size_t j=0; j<size; j++)   
		{  
			if(_temp[j])   
			{   
				layer_dims[pos++] = _temp[j] * j;   
			}  
		}  
		layer_comm.push_back(boost::mpi::cartesian_communicator(grid_comm, layer_dims));                    
	}  
  
	std::vector<std::vector<int>> displs(size);  
	std::vector<std::vector<int>> send_recv_counts(size);  
  
        std::vector<int> _t1 = {0,5,10};  
        std::vector<int> _t2 = {5,5,5};  
       std::array<int, size> _cnt = {10, 10, 10};  
      
        for(std::size_t i=0; i<size; i++)  
        {  
             for(std::size_t j=0; j<size; j++)  
            {  
                  displs[i].push_back(_t1[j]);  
                  send_recv_counts[i].push_back(_t2[j]);  
             }  
       }  
       for(std::size_t i=0; i<size; i++)  
       {  
             boost::mpi::scatterv(layer_comm[i], matrix_in[i].data(), send_recv_counts[i], displs[i], matrix_out[i].data(), _cnt[i], 0);  
        }  
  
       return 0;  
}  
	  
```  
  
Edit : Corrected to be more functional and find the problem easier.

---

## Comment 3

> Username: aminiussi  
> Created at: 2019-10-21 08:48:42 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544415092  

Thanks, I'm installng Eigen (3.3.7)

---

## Comment 4

> Username: aminiussi  
> Created at: 2019-10-21 15:33:11 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544570824  

Ok, I need to add a test to catch that one.  
  
Do you have the possibility to add the missing detail in $BOOST_ROOT/include/boost/mpi/collectives/scatterv.hpp:104:  
```  
template<typename T>  
void  
scatterv(const communicator& comm, const T* in_values,  
         const std::vector<int>& sizes, const std::vector<int>& displs,  
         T* out_values, int out_size, int root)  
{  
  using detail::c_data;  
  detail::scatterv_impl(comm, in_values, out_values, out_size, c_data(sizes), c_data(displs),  
                        root, is_mpi_datatype<T>());  
}  
```  
?  
  
Otherwise, it should be possible to add an header to inject boost::mpi::detail::scaterv_impl into boost::mpi.  
With boost_fix.hpp:  
```  
namepace boost { namespace mpi;  
 using detail::scatterv_impl;  
}}  
```  
  
and   
  
```  
#include <boost/mpi/communicator.hpp>  
#include <boost/mpi/collectives.hpp>  
#include "boost_fix.hpp"  
#include <boost/mpi/environment.hpp>  
#include <boost/mpi/cartesian_communicator.hpp>  
#include "Eigen/Dense"  
```  
  
Sorry for the inconvenience.

---

## Comment 5

> Username: GeorgeLykoudis  
> Created at: 2019-10-22 07:32:30 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544839706  

I used the first one and it worked. Thanks!

---

## Comment 6

> Username: aminiussi  
> Created at: 2019-10-22 14:17:25 UTC  
> Url: https://github.com/boostorg/mpi/issues/94#issuecomment-544985190  

The fix is in master.
