# #745 - threefry random number generator broken [Closed]

> Username: Ulfgard  
> Created at: 2017-10-08 10:44:21 UTC  
> Updated at: 2017-10-14 09:12:13 UTC  
> Closed at: 2017-10-14 09:12:13 UTC  
> Url: https://github.com/boostorg/compute/issues/745  

Hi,  
There are 2 issues.  
  
1. Threefry does not adhere to the interface of generators. The following program will not compile:  
  
```  
#include <boost/compute/system.hpp>  
#include <boost/compute/random/threefry_engine.hpp>  
#include <boost/compute/random/normal_distribution.hpp>  
#include <boost/compute/random/uniform_real_distribution.hpp>  
  
namespace compute = boost::compute;  
int main()  
{  
	// get default device and setup context  
	compute::device gpu = compute::system::default_device();  
	compute::context context(gpu);  
	compute::command_queue queue(context, gpu);  
	compute::threefry_engine<> random_engine(queue);  
	  
	compute::uniform_real_distribution<float> random_distribution(0.f, 4.f); //does not compile  
	// compute::normal_distribution<float> random_distribution(0.f, 4.f); // compiles  
	  
	compute::vector<float> random_values(10, 0.0,queue);  
	  
	random_distribution.generate(  
		random_values.begin(), random_values.end(), random_engine, queue  
	);  
}  
```  
  
2. Threefry does not have any source of entropy and adding the following to the code above will print out two times the same sequence:  
  
```  
    for(std::size_t i = 0; i != 2; ++i){  
		compute::vector<compute::uint_> random_values(10, 0.0,queue);  
		  
		random_engine.generate(  
			random_values.begin(), random_values.end(), queue  
		);  
		  
		std::vector<compute::uint_> values_cpu(10);  
		boost::compute::copy(random_values.begin(),random_values.end(), values_cpu.begin(), queue);  
		  
		for(std::size_t i = 0; i != 10; ++i){  
			std::cout<<values_cpu[i]<<" ";  
		}  
		std::cout<<std::endl;  
	}  
```  
3. This behaviour is not observed when generating normally distributed numbers from the generator. This is because the input sequence to the generator is not initialized and thus the generator relies on uninitialized memory, see https://github.com/boostorg/compute/blob/d36ef02b7860b9bcf5ea4911b77f1aa77b5f4059/include/boost/compute/random/normal_distribution.hpp#L91  
  
How to fix this:  
Threefry relies on its key as source of entropy and then generates the random numbers from a simple counting sequence 1,2,3.. . So an easy solution would be to use a mt19937 on cpu to generate a key and set it as kernel argument. This would also allow seeding through the mt19937. Then, within the kernel the function threefry2x32_R should be given the number of the current random number as first and the key as second argument. It is okay if the sequence starts every time from 0, as threefry would still be able to generate 2^64 different sequences. If this is not enough, either use threefry4x32_R for 2^128 different sequences or initialize the counter to some random offset.

---

## Comment 1

> Username: Ulfgard  
> Created at: 2017-10-08 10:52:16 UTC  
> Updated at: 2017-10-08 10:54:36 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-334998303  

2. is actually worse, this is the first line of the output:  
1797259609 2579123966 1797259609 2579123966 1797259609 2579123966 1797259609 2579123966 1797259609 2579123966   
  
see how it is two numbers alternating?

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-10-08 11:13:36 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-334999407  

I've been thinking about reimplementing it or introducing working Philox  
RNG. I'll take a look at it this week.  
  
08.10.2017 12:52 PM "Oswin Krause" <notifications@github.com> napisał(a):  
  
>  
>    1. is actually worse, this is the first line of the output:  
>    1.93434e+26 -1.92639e-23 1.93434e+26 -1.92639e-23 1.93434e+26  
>    -1.92639e-23 1.93434e+26 -1.92639e-23 1.93434e+26 -1.92639e-23  
>  
> see how it is two numbers alternating?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/compute/issues/745#issuecomment-334998303>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ACmCBMOFHEXgoxXH27153CO9IgBYl5poks5sqKligaJpZM4PxqcS>  
> .  
>

---

## Comment 3

> Username: Ulfgard  
> Created at: 2017-10-08 15:12:21 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-335013183  

i'd be happily offering a quick code check if you want

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-10-08 18:38:07 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-335028448  

Thanks, when I have something I'll make a push request, so you can review it. Are you more interested in Threefry or Philox, or you just need fast RNG?

---

## Comment 5

> Username: Ulfgard  
> Created at: 2017-10-08 20:20:03 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-335035137  

I mostly checked the threefry implementation in boost.compute to compare it with the Shaw-paper. I unfortunately need to roll my own, so I wanted to make sure i understood it correctly. But I mostly need a fast Rng and the most interesting aspect of threefry/philox etc is that one can easily transform matrix-indices to random numbers, which makes a lot of kernel logic very simple.

---

## Comment 6

> Username: jszuppe  
> Created at: 2017-10-09 11:29:41 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-335131205  

> transform matrix-indices to random numbers, which makes a lot of kernel logic very simple.  
  
Can you elaborate?   
  
How I plan to implement Philox: one state (128-bit counter) for the generator, key is generated from seed given by a user (64-bit unsigned integer), in each thread generates 4 random numbers using counter, thread index, and key (we sum counter and thread idx to get unique counter for each thread), finally we update the counter increasing it by the number of generated values.

---

## Comment 7

> Username: Ulfgard  
> Created at: 2017-10-13 07:30:34 UTC  
> Updated at: 2017-10-13 07:30:46 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-336373686  

The pull request seems okay. Nice improvement.   
  
  
> Can you elaborate?  
  
I would implement it as outlined above by picking a random key and use a counter starting from 0.   
This allows to use a nice property of the counter based rng: if you want to generate N random numbers, then the subsequences i,i+N,i+2*N... (i=0...N-1) are sequences of independent random numbers. This allows to implement rejection sampling extremely efficiently in kernel by just letting the kernel reject (compare with the discrete distribution). I would also favour a design where only one kernel is run to generate a random number instead of first generating a vector of 32 bit uint numbers which are then transformed in another kernel call. This could be done by having one program that has kernels to generate all supported types of random numbers (but at least the uniform distributions)  
  
I am currently playing around with a very basic implementation of this:  
https://github.com/Shark-ML/Remora/blob/gpu_kernel_rework/include/remora/kernels/gpu/random.hpp

---

## Comment 8

> Username: jszuppe  
> Created at: 2017-10-13 09:06:14 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-336395264  

>  I would also favour a design where only one kernel is run to generate a random number instead of first generating a vector of 32 bit uint numbers which are then transformed in another kernel call. This could be done by having one program that has kernels to generate all supported types of random numbers (but at least the uniform distributions)  
  
Indeed, that would save some performance. Maybe I'll do that late (because I'd have to do this update for every RNG in BC).

---

## Comment 9

> Username: jszuppe  
> Created at: 2017-10-14 09:12:13 UTC  
> Url: https://github.com/boostorg/compute/issues/745#issuecomment-336622168  

Fixed by #747
