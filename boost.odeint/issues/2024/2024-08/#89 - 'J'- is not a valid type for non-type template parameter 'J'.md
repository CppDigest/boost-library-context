# #89 - 'J': is not a valid type for non-type template parameter 'J' [Open]

> Username: BennyRod  
> Created at: 2024-08-19 18:56:04 UTC  
> Updated at: 2024-08-23 00:02:45 UTC  
> Url: https://github.com/boostorg/odeint/issues/89  

Hello,  
I have been facing an issue using Boost Odeint with CUDA in Windows 11then I am going to include a MVCE using a .cu file:  
  
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
  
Error C2334 unexpected token(s) preceding '{'; skipping apparent function body CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 246  
2.Error C2993 'J': is not a valid type for non-type template parameter 'J' CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 236  
  
Error C2062 type 'unknown-type' unexpected CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 236  
  
Error C2334 unexpected token(s) preceding '{'; skipping apparent function body CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 239  
  
5.Error C2993 'J': is not a valid type for non-type template parameter 'J' CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 243  
  
6.Error C4430 missing type specifier - int assumed. Note: C++ does not support default-int CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 243  
  
7.Error C2062 type 'unknown-type' unexpected CudaRuntime1 C:\Users\benny\Downloads\boost_1_86_0\boost\fusion\container\vector\vector.hpp 243  
  
The first error refers to the use of a C++ version prior to C++ 20 but I have used C++ 20 and it is the same error.  
  
On the other hand, if it is replaced "odeint" by "interval" then the code compiles properly.  
  
```  
#include <boost/numeric/odeint.hpp>  
int main()  
{  
  
}  
```  
Output:  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 11:07 AM and took 01.917 seconds ==========  
  
Finally, this code works properly in Ubuntu using CMake then it is not a general incompatibility between CUDA and BOOST.  
  
*I have already published this error in fusion but I was referred to Odeint: https://github.com/boostorg/fusion/issues/269  
   
I hope you can help us with this issue. Thank you so much in advance! Let me know if there is something I can do to help you to fix this error.

---

## Comment 1

> Username: mborland  
> Created at: 2024-08-19 19:01:44 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297242198  

You will have to use the thrust adaptor: https://www.boost.org/doc/libs/1_86_0/libs/numeric/odeint/doc/html/boost_numeric_odeint/tutorial/using_cuda__or_openmp__tbb_______via_thrust.html. You cannot directly use NVCC since that would require all dependent libraries to support CUDA (which is the issue you are seeing pop up from Fusion).

---

## Comment 2

> Username: BennyRod  
> Created at: 2024-08-19 19:28:21 UTC  
> Updated at: 2024-08-19 19:30:26 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297288626  

Thank you so much for your response! Could you explain me why this code works in Ubuntu using a CMakeLists.txt? I have included the CMakeLists that I am using. Thank you so much in advance.  
  
```  
# A CMake version 3.10 or higher is required   
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)  
  
# set the project language to C++  
project(main LANGUAGES CUDA CXX)  
  
# set all targets to use C++14  
set(CMAKE_CXX_STANDARD 14)  
  
# it is an error if C++14 is not available   
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
  
# set the CUDA architectures  
#set(CMAKE_CUDA_ARCHITECTURES 60 70 75 80)  
  
# set the output directory for executable files  
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/../bin")  
  
#store the local location of Boost   
set(BOOST_ROOT "/usr/local/apps/boost/1.84/include/")  
  
# add the local include directory to the include path  
include_directories(${BOOST_ROOT})  
  
# find Boost library  
find_package(Boost 1.84.0 REQUIRED)  
  
# Add CUDA files to the project  
set(CUDA_FILES  
     main.cu  
)  
  
# Tell CMake to compile the CUDA files  
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-O3)  
  
#create the executable  
add_executable(main ${CUDA_FILES} )  
  
# allow separate compilation. it is neccessary for multiple .cu files  
set_target_properties(main PROPERTIES CUDA_SEPARABLE_COMPILATION ON)  
```

---

## Comment 3

> Username: mborland  
> Created at: 2024-08-19 19:36:45 UTC  
> Updated at: 2024-08-19 19:37:00 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297303065  

Presumably Ubuntu is using GCC as the host compiler. Sometimes invalid code (e.g. not marked with ```__device__```) will compile just fine, but then at run time you'll get all kinds of failures. Since you're not calling any functions it's like it's not instantiating any templates for you to see an error.

---

## Comment 4

> Username: BennyRod  
> Created at: 2024-08-19 20:45:26 UTC  
> Updated at: 2024-08-19 20:50:54 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297424913  

I have been testing one of the Odeint examples, the Harmonic Oscillator: https://github.com/headmyshoulder/odeint-v2/blob/master/examples/harmonic_oscillator.cpp  
It was compiled using the C++ and nvcc compiler individually but in both cases, it compiled and runned properly.  Also, the numeric results were the same!  
I have included the used CMakeLists.txt in both cases and the outputs. Please, letme know what do you think about this, I am really interested in using Odeint in Windows.  
  
HarmonicOscillator:  
```  
/*  
Copyright 2010-2012 Karsten Ahnert  
Copyright 2011-2013 Mario Mulansky  
Copyright 2013 Pascal Germroth  
  
Distributed under the Boost Software License, Version 1.0.  
(See accompanying file LICENSE_1_0.txt or  
copy at http://www.boost.org/LICENSE_1_0.txt)  
*/  
  
#include <iostream>  
#include <vector>  
  
#include <boost/numeric/odeint.hpp>  
  
//[ rhs_function  
/* The type of container used to hold the state vector */  
typedef std::vector<double> state_type;  
  
const double gam = 0.15;  
  
/* The rhs of x' = f(x) */  
void harmonic_oscillator(const state_type &x, state_type &dxdt, const double /* t */)  
{  
   dxdt[0] = x[1];  
   dxdt[1] = -x[0] - gam * x[1];  
}  
//]  
  
//[ rhs_class  
/* The rhs of x' = f(x) defined as a class */  
class harm_osc  
{  
  
   double m_gam;  
  
public:  
   harm_osc(double gam) : m_gam(gam) {}  
  
   void operator()(const state_type &x, state_type &dxdt, const double /* t */)  
   {  
       dxdt[0] = x[1];  
       dxdt[1] = -x[0] - m_gam * x[1];  
   }  
};  
//]  
  
//[ integrate_observer  
struct push_back_state_and_time  
{  
   std::vector<state_type> &m_states;  
   std::vector<double> &m_times;  
  
   push_back_state_and_time(std::vector<state_type> &states, std::vector<double> &times)  
       : m_states(states), m_times(times) {}  
  
   void operator()(const state_type &x, double t)  
   {  
       m_states.push_back(x);  
       m_times.push_back(t);  
   }  
};  
//]  
  
struct write_state  
{  
   void operator()(const state_type &x) const  
   {  
       std::cout << x[0] << "\t" << x[1] << "\n";  
   }  
};  
  
int main(int /* argc */, char ** /* argv */)  
{  
   using namespace std;  
   using namespace boost::numeric::odeint;  
  
   //[ state_initialization  
   state_type x(2);  
   x[0] = 1.0; // start at x=1.0, p=0.0  
   x[1] = 0.0;  
   //]  
  
   //[ integration  
   size_t steps = integrate(harmonic_oscillator,  
                            x, 0.0, 10.0, 0.1);  
   //]  
  
   //[ integration_class  
   harm_osc ho(0.15);  
   steps = integrate(ho,  
                     x, 0.0, 10.0, 0.1);  
   //]  
  
   //[ integrate_observ  
   vector<state_type> x_vec;  
   vector<double> times;  
  
   steps = integrate(harmonic_oscillator,  
                     x, 0.0, 10.0, 0.1,  
                     push_back_state_and_time(x_vec, times));  
  
   /* output */  
   for (size_t i = 0; i <= steps; i++)  
   {  
       cout << times[i] << '\t' << x_vec[i][0] << '\t' << x_vec[i][1] << '\n';  
   }  
   //]  
  
   //[ define_const_stepper  
   runge_kutta4<state_type> stepper;  
   integrate_const(stepper, harmonic_oscillator, x, 0.0, 10.0, 0.01);  
   //]  
  
   //[ integrate_const_loop  
   const double dt = 0.01;  
   for (double t = 0.0; t < 10.0; t += dt)  
       stepper.do_step(harmonic_oscillator, x, t, dt);  
   //]  
  
   //[ define_adapt_stepper  
   typedef runge_kutta_cash_karp54<state_type> error_stepper_type;  
   //]  
  
   //[ integrate_adapt  
   typedef controlled_runge_kutta<error_stepper_type> controlled_stepper_type;  
   controlled_stepper_type controlled_stepper;  
   integrate_adaptive(controlled_stepper, harmonic_oscillator, x, 0.0, 10.0, 0.01);  
   //]  
  
   {  
       //[integrate_adapt_full  
       double abs_err = 1.0e-10, rel_err = 1.0e-6, a_x = 1.0, a_dxdt = 1.0;  
       controlled_stepper_type controlled_stepper(  
           default_error_checker<double, range_algebra, default_operations>(abs_err, rel_err, a_x, a_dxdt));  
       integrate_adaptive(controlled_stepper, harmonic_oscillator, x, 0.0, 10.0, 0.01);  
       //]  
   }  
  
   //[integrate_adapt_make_controlled  
   integrate_adaptive(make_controlled<error_stepper_type>(1.0e-10, 1.0e-6),  
                      harmonic_oscillator, x, 0.0, 10.0, 0.01);  
   //]  
  
   //[integrate_adapt_make_controlled_alternative  
   integrate_adaptive(make_controlled(1.0e-10, 1.0e-6, error_stepper_type()),  
                      harmonic_oscillator, x, 0.0, 10.0, 0.01);  
   //]  
  
#ifdef BOOST_NUMERIC_ODEINT_CXX11  
   //[ define_const_stepper_cpp11  
   {  
       runge_kutta4<state_type> stepper;  
       integrate_const(stepper, [](const state_type &x, state_type &dxdt, double t)  
                       {  
           dxdt[0] = x[1]; dxdt[1] = -x[0] - gam*x[1]; }, x, 0.0, 10.0, 0.01);  
   }  
   //]  
  
   //[ harm_iterator_const_step]  
   std::for_each(make_const_step_time_iterator_begin(stepper, harmonic_oscillator, x, 0.0, 0.1, 10.0),  
                 make_const_step_time_iterator_end(stepper, harmonic_oscillator, x),  
                 [](std::pair<const state_type &, const double &> x)  
                 { cout << x.second << " " << x.first[0] << " " << x.first[1] << "\n"; });  
//]  
#endif  
}  
```  
  
C++:  
```  
  
# A CMake version 3.10 or higher is required   
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)  
  
# set the project language to C++  
project(main LANGUAGES CUDA CXX)  
  
# set all targets to use C++14  
set(CMAKE_CXX_STANDARD 14)  
  
# it is an error if C++14 is not available   
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
  
# set the CUDA architectures  
#set(CMAKE_CUDA_ARCHITECTURES 60 70 75 80)  
  
# set the output directory for executable files  
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/../bin")  
  
#store the local location of Boost   
set(BOOST_ROOT "/usr/local/apps/boost/1.84/include/")  
  
# add the local include directory to the include path  
include_directories(${BOOST_ROOT})  
  
# find Boost library  
find_package(Boost 1.84.0 REQUIRED)  
  
# Add CUDA files to the project  
set(CPP_FILES  
     main.cpp  
)  
  
# Tell CMake to compile the CUDA files  
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-O3)  
  
#create the executable  
add_executable(main ${CPP_FILES} )  
  
# allow separate compilation. it is neccessary for multiple .cu files  
set_target_properties(main PROPERTIES CUDA_SEPARABLE_COMPILATION ON)  
```  
Output:  
![Captura de pantalla 2024-08-19 153120](https://github.com/user-attachments/assets/1052abca-fc20-4ef3-814a-08d41b53a51c)  
  
CUDA:  
```  
  
# A CMake version 3.10 or higher is required   
cmake_minimum_required(VERSION 3.10 FATAL_ERROR)  
  
# set the project language to C++  
project(main LANGUAGES CUDA CXX)  
  
# set all targets to use C++14  
set(CMAKE_CXX_STANDARD 14)  
  
# it is an error if C++14 is not available   
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
  
# set the CUDA architectures  
#set(CMAKE_CUDA_ARCHITECTURES 60 70 75 80)  
  
# set the output directory for executable files  
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/../bin")  
  
#store the local location of Boost   
set(BOOST_ROOT "/usr/local/apps/boost/1.84/include/")  
  
# add the local include directory to the include path  
include_directories(${BOOST_ROOT})  
  
# find Boost library  
find_package(Boost 1.84.0 REQUIRED)  
  
# Add CUDA files to the project  
set(CUDA_FILES  
     main.cu  
)  
  
# Tell CMake to compile the CUDA files  
set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-O3)  
  
#create the executable  
add_executable(main ${CUDA_FILES} )  
  
# allow separate compilation. it is neccessary for multiple .cu files  
set_target_properties(main PROPERTIES CUDA_SEPARABLE_COMPILATION ON)  
```  
  
![Captura de pantalla 2024-08-19 153353](https://github.com/user-attachments/assets/e932f75f-ed10-4932-b3be-e96087a1a074)

---

## Comment 5

> Username: mborland  
> Created at: 2024-08-19 20:55:33 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297440677  

You need to run one of the examples out of this folder: https://github.com/headmyshoulder/odeint-v2/tree/master/examples/thrust. The results are the same since no CUDA was used.

---

## Comment 6

> Username: BennyRod  
> Created at: 2024-08-19 21:16:25 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297471620  

Thank you for your continuous responses! My point is that even not using any CUDA code, the example does not compile on Window, but it does on Ubuntu.  As you pointed out before, the host code must be compiled using the C++ compiler (this is why both outputs are the same) then I don't need to use Thrust because I don't have any CUDA code. I hope my point is clearer now.

---

## Comment 7

> Username: mborland  
> Created at: 2024-08-19 21:26:49 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297486898  

> Thank you for your continuous responses! My point is that even not using any CUDA code, the example does not compile on Window, but it does on Ubuntu. As you pointed out before, the host code must be compiled using the C++ compiler (this is why both outputs are the same) then I don't need to use Thrust because I don't have any CUDA code. I hope my point is clearer now.  
  
Unfortunately I have no experience with CUDA on windows. I have only ever run it on Ubuntu and Red Hat platforms.

---

## Comment 8

> Username: BennyRod  
> Created at: 2024-08-19 21:28:25 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2297488997  

Ok no problem. I would like to thank you because you have been very kind! Have a great day!

---

## Comment 9

> Username: BennyRod  
> Created at: 2024-08-23 00:02:44 UTC  
> Url: https://github.com/boostorg/odeint/issues/89#issuecomment-2305939586  

I have a brute force solution: https://github.com/boostorg/fusion/issues/269
