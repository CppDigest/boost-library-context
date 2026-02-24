# #49 - Using multicores with OpenMP and Coroutine2 [Open]

> Username: PierrickPochelu  
> Created at: 2024-02-29 08:51:49 UTC  
> Updated at: 2024-02-29 08:51:49 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/49  

Hello,  
  
I am currently modeling multiple producers using Boost.Coroutine2 and storing them in a container (std::vector). My goal is to leverage OpenMP to accelerate the calls of these coroutines on multiple cores.  
  
However, I am encountering an issue where only one core is running the program. I have been searching for documentation on the combination of OpenMP and Coroutine2 but have not found examples or advice.  
  
Below is a simplified version of my code:  
  
```  
#include <iostream>  
#include <boost/coroutine2/all.hpp>  
#include <boost/function.hpp>  
#include <boost/bind.hpp>  
#define N 10000000  
  
using namespace boost::coroutines2;  
using CoroutinePush = coroutine<void>::push_type;  
using CoroutinePull = coroutine<void>::pull_type;  
using Funptr = boost::function<void(CoroutinePush&)>;  
  
class Env{  
        public:  
        long x=0;  
};  
  
  
void producer(CoroutinePush& yield, Env* env) {  
    yield(); // init  
    for (int i = 0; i < N; ++i) {  
        env->x++; // some work here  
        yield();  // Yield the produced value  
    }  
}  
  
  
int main() {  
    Env* env=new Env();  
    Funptr producerFunction = boost::bind(&producer, _1, env);  
  
    std::vector<CoroutinePull*> l;  
    for(int i=0;i<N;i++){  
        l.push_back(new CoroutinePull(producerFunction));  
    }  
  
  
    #pragma omp parallel for  
    for(int i=0;i<N;i++){  
            // #pragma omp critical // if it's uncommented  x becomes predictable but 1 thread at a time  
            {  
            (*l[i])();  
            }  
    }  
  
    std::cout << "x:" << env->x << std::endl;  
  
  
    for(int i = 0; i < N; i++) {  
        delete l[i];  
    }  
    delete env;  
    return 0;  
}  
```  
  
  
My compilation line: `g++ -std=c++20 -fopenmp ./g.cpp  -lboost_context -lboost_coroutine`  
  
I would greatly appreciate any guidance, examples, or advice on how to use Boost.Coroutine2 and OpenMP for multicore execution.   
  
Thank you
