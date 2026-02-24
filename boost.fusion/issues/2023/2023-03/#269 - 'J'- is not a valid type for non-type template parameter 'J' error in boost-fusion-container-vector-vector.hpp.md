# #269 - 'J': is not a valid type for non-type template parameter 'J' error in boost\fusion\container\vector\vector.hpp [Open]

> Username: DanielMueller1996  
> Created at: 2023-03-12 13:11:32 UTC  
> Updated at: 2024-08-23 00:01:42 UTC  
> Url: https://github.com/boostorg/fusion/issues/269  

I am using Visual Studio 2022 and am currently trying to compile the following code:  
```c++  
  
#include <iostream>  
#include <cmath>  
  
#include <thrust/device_vector.h>  
#include <thrust/iterator/permutation_iterator.h>  
#include <thrust/iterator/counting_iterator.h>  
  
#include <boost/numeric/odeint/stepper/runge_kutta4.hpp>  
#include <boost/numeric/odeint/integrate/integrate_const.hpp>  
#include <boost/numeric/odeint/external/thrust/thrust.hpp>  
  
using namespace std;  
  
using namespace boost::numeric::odeint;  
  
  
//change this to float if your device does not support double computation  
typedef double value_type;  
  
  
//[ thrust_phase_chain_system  
//change this to host_vector< ... > if you want to run on CPU  
typedef thrust::device_vector< value_type > state_type;  
typedef thrust::device_vector< size_t > index_vector_type;  
//typedef thrust::host_vector< value_type > state_type;  
//typedef thrust::host_vector< size_t > index_vector_type;  
  
  
class phase_oscillators  
{  
  
public:  
  
    struct sys_functor  
    {  
        template< class Tuple >  
        __host__ __device__  
            void operator()(Tuple t)  // this functor works on tuples of values  
        {  
            // first, unpack the tuple into value, neighbors and omega  
            const value_type phi = thrust::get<0>(t);  
            const value_type phi_left = thrust::get<1>(t);  // left neighbor  
            const value_type phi_right = thrust::get<2>(t); // right neighbor  
            const value_type omega = thrust::get<3>(t);  
            // the dynamical equation  
            thrust::get<4>(t) = omega + sin(phi_right - phi) + sin(phi - phi_left);  
        }  
    };  
  
    phase_oscillators(const state_type& omega)  
        : m_omega(omega), m_N(omega.size()), m_prev(omega.size()), m_next(omega.size())  
    {  
        // build indices pointing to left and right neighbours  
        thrust::counting_iterator<size_t> c(0);  
        thrust::copy(c, c + m_N - 1, m_prev.begin() + 1);  
        m_prev[0] = 0; // m_prev = { 0 , 0 , 1 , 2 , 3 , ... , N-1 }  
  
        thrust::copy(c + 1, c + m_N, m_next.begin());  
        m_next[m_N - 1] = m_N - 1; // m_next = { 1 , 2 , 3 , ... , N-1 , N-1 }  
    }  
  
    void operator() (const state_type& x, state_type& dxdt, const value_type dt)  
    {  
        thrust::for_each(  
            thrust::make_zip_iterator(  
                thrust::make_tuple(  
                    x.begin(),  
                    thrust::make_permutation_iterator(x.begin(), m_prev.begin()),  
                    thrust::make_permutation_iterator(x.begin(), m_next.begin()),  
                    m_omega.begin(),  
                    dxdt.begin()  
                )),  
            thrust::make_zip_iterator(  
                thrust::make_tuple(  
                    x.end(),  
                    thrust::make_permutation_iterator(x.begin(), m_prev.end()),  
                    thrust::make_permutation_iterator(x.begin(), m_next.end()),  
                    m_omega.end(),  
                    dxdt.end())),  
            sys_functor()  
        );  
    }  
  
private:  
  
    const state_type& m_omega;  
    const size_t m_N;  
    index_vector_type m_prev;  
    index_vector_type m_next;  
};  
//]  
  
const size_t N = 32768;  
const value_type pi = 3.1415926535897932384626433832795029;  
const value_type epsilon = 6.0 / (N * N); // should be < 8/N^2 to see phase locking  
const value_type dt = 0.1;  
  
int main(int arc, char* argv[])  
{  
    //[ thrust_phase_chain_integration  
    // create initial conditions and omegas on host:  
    vector< value_type > x_host(N);  
    vector< value_type > omega_host(N);  
    for (size_t i = 0; i < N; ++i)  
    {  
        x_host[i] = 2.0 * pi * rand();  
        omega_host[i] = (N - i) * epsilon; // decreasing frequencies  
    }  
  
    // copy to device  
    state_type x = x_host;  
    state_type omega = omega_host;  
  
    // create stepper  
    runge_kutta4< state_type, value_type, state_type, value_type > stepper;  
  
    // create phase oscillator system function  
    phase_oscillators sys(omega);  
  
    // integrate  
    integrate_const(stepper, sys, x, 0.0, 10.0, dt);  
  
    thrust::copy(x.begin(), x.end(), std::ostream_iterator< value_type >(std::cout, "\n"));  
    std::cout << std::endl;  
    //]  
}  
```  
An error occurs upon compilation using VS22 community edition in `vector.hpp`.  
  
Boost Version: 1.81  
Platform: Windows 10 64x  
  
This uses Thrust functionality to calculate a large amount of ODEs.  
  
The error is:  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2993	'J': is not a valid type for non-type template parameter 'J'	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	236	  
Error	C2062	type 'unknown-type' unexpected	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	236	  
Error	C2334	unexpected token(s) preceding '{'; skipping apparent function body	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	239	  
Error	C2993	'J': is not a valid type for non-type template parameter 'J'	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	243	  
Error	C4430	missing type specifier - int assumed. Note: C++ does not support default-int	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	243	  
Error	C2062	type 'unknown-type' unexpected	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	243	  
Error	C2334	unexpected token(s) preceding '{'; skipping apparent function body	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	246	  
Error	C2504	'boost::fusion::vector_detail::vector_data<std::integer_sequence<unsigned __int64,0,1,2,3>,boost::numeric::odeint::detail::stage<T,1>,boost::numeric::odeint::detail::stage<T,2>,boost::numeric::odeint::detail::stage<T,3>,boost::numeric::odeint::detail::stage<T,4>>': base class undefined	Pairproduction	C:\Program Files\Boost\boost_1_81_0\boost\fusion\container\vector\vector.hpp	253	  
Error	MSB3721	The command ""C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\bin\nvcc.exe" -gencode=arch=compute_52,code=\"sm_52,compute_52\" --use-local-env -ccbin "G:\Visual Studio\VC\Tools\MSVC\14.34.31933\bin\HostX64\x64" -x cu   -I"C:\Program Files\Boost\boost_1_81_0" -I"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\include" -I"C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.0\include"  -G   --keep-dir x64\Debug  -maxrregcount=0  --machine 64 --compile -cudart static  -g  -DWIN32 -DWIN64 -D_DEBUG -D_CONSOLE -D_MBCS -Xcompiler "/EHsc /W3 /nologo /Od /Fdx64\Debug\vc143.pdb /FS /Zi /RTC1 /MDd " -o "C:\Users\danie\OneDrive\Dokumente\schule\studium\7. Semester\Spezialisierung\Spezialisierung_code\Pairproduction\x64\Debug\kernel.cu.obj" "C:\Users\danie\OneDrive\Dokumente\schule\studium\7. Semester\Spezialisierung\Spezialisierung_code\Pairproduction\kernel.cu"" exited with code 2.	Pairproduction	G:\Visual Studio\MSBuild\Microsoft\VC\v170\BuildCustomizations\CUDA 12.0.targets	794  
```  
  
What is wrong and can i fix it on my end?

---

## Comment 1

> Username: RKGalaxy-Luo  
> Created at: 2024-05-11 09:09:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2105647472  

I've tried such a method:  
correct the code in "boost\fusion\container\vector\vector.hpp"  
![Tried](https://github.com/boostorg/fusion/assets/104077812/a078c00c-7645-4789-afe4-fc83f7e66438)  
  
  
  
private:  
    template <std::size_t J>  
    using store_at = decltype(store_at_impl<J>(static_cast<vector_data*>(nullptr)));  
  
public:  
    template <std::size_t J>  
    BOOST_CXX14_CONSTEXPR BOOST_FUSION_GPU_ENABLED  
    typename store_at<J>::elem_type& at_impl(std::integral_constant<std::size_t, J>)  
    {  
        return store_at<J>::elem;  
    }  
  
    template <std::size_t J>  
    BOOST_CONSTEXPR BOOST_FUSION_GPU_ENABLED  
    typename store_at<J>::elem_type const& at_impl(std::integral_constant<std::size_t, J>) const  
    {  
        return store_at<J>::elem;  
    }  
};  
  
NOTICE: I don't know whether such a method will have an influence on other libraries

---

## Comment 2

> Username: djowel  
> Created at: 2024-05-11 09:36:57 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2105654183  

If you think this is a fusion issue, please post an MVCE (without any dependencies). I have no expertise on the other dependent libraries.

---

## Comment 3

> Username: lingyuanmoshang  
> Created at: 2024-07-18 09:53:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2236099435  

Has this issue been solved？

---

## Comment 4

> Username: djowel  
> Created at: 2024-07-18 10:11:08 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2236134801  

> Has this issue been solved？  
  
Again, please post an MVCE https://stackoverflow.com/help/minimal-reproducible-example

---

## Comment 5

> Username: BennyRod  
> Created at: 2024-08-15 16:10:36 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2291618669  

Hello,   
I have been facing the same issue then I am going to include a MVCE using a .cu file:  
  
Configurations:  
-Microsoft Visual Studio Community 2022 (64-bit) - Current  
Version 17.11.0  
-Nvidia Driver Version: 560.81  
-CUDA Toolkit 12.6  
-BOOST 1.86  
-MVCE.cu  
  
MVCE.cu:  
```  
#include <boost/numeric/odeint.hpp>  
int main()  
{  
  
}  
```  
Output:  
  
1. Error	C2334	unexpected token(s) preceding '{'; skipping apparent function body	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	246		  
  
2.Error	C2993	'J': is not a valid type for non-type template parameter 'J'	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	236		  
  
3. Error	C2062	type 'unknown-type' unexpected	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	236	  
	  
4. Error	C2334	unexpected token(s) preceding '{'; skipping apparent function body	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	239		  
  
5.Error	C2993	'J': is not a valid type for non-type template parameter 'J'	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	243		  
  
6.Error	C4430	missing type specifier - int assumed. Note: C++ does not support default-int	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	243		  
  
7.Error	C2062	type 'unknown-type' unexpected	CudaRuntime1	C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp	243		  
  
The first error refers to the use of a C++ version prior to C++ 20 but I have used C++ 20 and it is the same error.  
  
On the other hand, if it is replaced odeint by interval then the code compiles properly.  
  
```  
#include <boost/numeric/odeint.hpp>  
int main()  
{  
  
}  
```  
Output:  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 11:07 AM and took 01.917 seconds ==========  
  
Finally, this code works properly in Ubuntu using CMake then it is not a general incompatibility between CUDA and BOOST. I hope you can help us with this issue. Thank you so much in advance! Let me know if there is something I can do to help you to fix this error.

---

## Comment 6

> Username: djowel  
> Created at: 2024-08-15 22:13:45 UTC  
> Updated at: 2024-08-15 22:14:56 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2292362623  

> #include <boost/numeric/odeint.hpp>  
  
This is not directly a Boost.Fusion issue, and should probably be redirected to Boost.Numeric.Odeint instead.

---

## Comment 7

> Username: BennyRod  
> Created at: 2024-08-23 00:01:19 UTC  
> Updated at: 2024-08-23 00:01:42 UTC  
> Url: https://github.com/boostorg/fusion/issues/269#issuecomment-2305938300  

Hello:  
On Windows 11, I have found that if you comment out these lines  from vector.hpp the code works properly. In my case, I am using the Boost library in the host then I do not need anything in the device. Definitely, there are some differences in how vector.hpp is treated on Windows and Ubuntu.  
  
![Captura de pantalla 2024-08-22 185436](https://github.com/user-attachments/assets/fbd5b417-8073-463a-82b7-13d25e95e5d2)  
  
Code using C++ compiler:  
```  
  
#include <boost/numeric/odeint.hpp>  
#include <iostream>  
#include <vector>  
  
using namespace boost::numeric::odeint;  
  
std::vector<std::vector<double>> variable;  
std::vector<double> times;  
  
void system_of_equations(const std::vector<double>& variables,  
	std::vector<double>& derivative_rates, const double time)  
{  
	derivative_rates[0] = variables[0] + variables[1];  
	derivative_rates[1] = variables[0] - variables[1];  
}  
  
void printVariables(const std::vector<double>& variables, const double time)  
{  
	std::cout << "t=" << time << "   "  
		<< "x=" << variables[0] << "   "  
		<< "y=" << variables[1] << std::endl;  
  
	variable.push_back(variables);  
	times.push_back(time);  
}  
  
int main()  
{  
	std::vector<double> initial_variables = { 1.0, 0.0 };   
  
	double start_time = 0.0, end_time = 5,  
		time_step = 1e-4;   
	integrate(system_of_equations, initial_variables, start_time, end_time, time_step, printVariables);  
  
	return 0;  
}  
```  
Output:  
```  
t=0   x=1   y=0  
t=0.0001   x=1.0001   y=0.0001  
t=0.00055   x=1.00055   y=0.00055  
t=0.002575   x=1.00258   y=0.00257501  
t=0.0116875   x=1.01182   y=0.011688  
t=0.0526938   x=1.05552   y=0.0527425  
t=0.23332   x=1.29251   y=0.237577  
t=0.413945   x=1.6143   y=0.437997  
t=0.605022   x=2.07053   y=0.681595  
t=0.796099   x=2.67888   y=0.975267  
t=0.987176   x=3.48403   y=1.34059  
t=1.17825   x=4.54515   y=1.8044  
t=1.36933   x=5.94017   y=2.40077  
t=1.56041   x=7.7716   y=3.17352  
t=1.75148   x=10.174   y=4.17941  
t=1.94256   x=13.3238   y=5.49234  
t=2.13364   x=17.4525   y=7.20878  
t=2.32471   x=22.8633   y=9.45482  
t=2.51579   x=29.9538   y=12.3955  
t=2.70687   x=39.2449   y=16.2468  
t=2.89794   x=51.4192   y=21.2916  
t=3.08902   x=67.371   y=27.9007  
t=3.2801   x=88.2723   y=36.5596  
t=3.47117   x=115.659   y=47.9043  
t=3.66225   x=151.542   y=62.7684  
t=3.85333   x=198.558   y=82.2438  
t=4.0444   x=260.162   y=107.761  
t=4.23548   x=340.879   y=141.196  
t=4.42656   x=446.639   y=185.003  
t=4.61763   x=585.212   y=242.402  
t=4.80871   x=766.778   y=317.609  
t=4.99979   x=1004.68   y=416.15  
t=5   x=1004.98   y=416.276  
```  
Code using C++ and NVCC:  
  
```  
#include<cuda_runtime.h>  
#include<stdio.h>  
#include <boost/numeric/odeint.hpp>  
#include <iostream>  
#include <vector>  
  
using namespace boost::numeric::odeint;  
  
std::vector<std::vector<double>> variable;  
std::vector<double> times;  
  
void system_of_equations(const std::vector<double>& variables,  
	std::vector<double>& derivative_rates, const double time)  
{  
	derivative_rates[0] = variables[0] + variables[1];  
	derivative_rates[1] = variables[0] - variables[1];  
}  
  
void printVariables(const std::vector<double>& variables, const double time)  
{  
	std::cout << "t=" << time << "   "  
		<< "x=" << variables[0] << "   "  
		<< "y=" << variables[1] << std::endl;  
  
	variable.push_back(variables);  
	times.push_back(time);  
}  
  
__global__ void hello()  
{  
	printf("Hello World!\n");  
}  
  
  
int main()  
{  
	std::vector<double> initial_variables = { 1.0, 0.0 };   
  
	double start_time = 0.0, end_time = 5,  
		time_step = 1e-4;   
  
	integrate(system_of_equations, initial_variables, start_time, end_time, time_step, printVariables);  
  
	int* a;  
	cudaMalloc(&a, 10 * sizeof(int));  
	cudaFree(a);  
  
	hello << <1, 10 >> > ();  
  
	cudaDeviceReset();  
  
	return 0;  
}  
  
```  
  
Output:  
```  
  
t=0   x=1   y=0  
t=0.0001   x=1.0001   y=0.0001  
t=0.00055   x=1.00055   y=0.00055  
t=0.002575   x=1.00258   y=0.00257501  
t=0.0116875   x=1.01182   y=0.011688  
t=0.0526938   x=1.05552   y=0.0527425  
t=0.23332   x=1.29251   y=0.237577  
t=0.413945   x=1.6143   y=0.437997  
t=0.605022   x=2.07053   y=0.681595  
t=0.796099   x=2.67888   y=0.975267  
t=0.987176   x=3.48403   y=1.34059  
t=1.17825   x=4.54515   y=1.8044  
t=1.36933   x=5.94017   y=2.40077  
t=1.56041   x=7.7716   y=3.17352  
t=1.75148   x=10.174   y=4.17941  
t=1.94256   x=13.3238   y=5.49234  
t=2.13364   x=17.4525   y=7.20878  
t=2.32471   x=22.8633   y=9.45482  
t=2.51579   x=29.9538   y=12.3955  
t=2.70687   x=39.2449   y=16.2468  
t=2.89794   x=51.4192   y=21.2916  
t=3.08902   x=67.371   y=27.9007  
t=3.2801   x=88.2723   y=36.5596  
t=3.47117   x=115.659   y=47.9043  
t=3.66225   x=151.542   y=62.7684  
t=3.85333   x=198.558   y=82.2438  
t=4.0444   x=260.162   y=107.761  
t=4.23548   x=340.879   y=141.196  
t=4.42656   x=446.639   y=185.003  
t=4.61763   x=585.212   y=242.402  
t=4.80871   x=766.778   y=317.609  
t=4.99979   x=1004.68   y=416.15  
t=5   x=1004.98   y=416.276  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
Hello World!  
```
