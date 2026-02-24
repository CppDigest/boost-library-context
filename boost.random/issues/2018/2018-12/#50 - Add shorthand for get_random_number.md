# #50 - Add shorthand for get_random_number [Closed]

> Username: Mike-Devel  
> Created at: 2018-12-16 13:08:13 UTC  
> Updated at: 2019-05-23 12:30:15 UTC  
> Closed at: 2019-05-13 12:14:28 UTC  
> Url: https://github.com/boostorg/random/issues/50  

I hope I haven't just overlooked it, but there seems to be no simple `get_random_number<int>(0,5)` function.   
  
Sure, in some situations I want very precises control over what engine and distribution objects are used, so I can make tradeoffs between performance, memory consumption and random number qualities/properties. But in many more scenarios I just want a random number with some reasonable tradeoffs (as a non-expert in the field, I can't really make a good decision anyway) and due to the relatively complex interface of Boost.Random (and its c++11 version), people are still using `rand` (in particular in tutorials) far too often.  
  
So would you consider adding a few convenience functions like get_random_number to this library? I'd be willing to make an according PR, but only if there is a realistic chance this gets merged.    
  
One of the major design questions I've seen in the past was how to make this thread-safe (either use thread_local storage or a mutex. I'd favor the use of a mutex/spinlock and a fast generator, because thread_local storage is not available in some (embedded) environments.  
  
I think there were also some papers about this to be discussed on the c++ committee, but I don't know what became of them, but maybe the design could follow whatever was proposed there  
.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-12-18 02:21:43 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-448072612  

AMDG  
  
On 12/16/2018 06:08 AM, Mike-Devel wrote:  
> I hope I haven't just overlooked it, but there seems to be no simple `get_random_number<int>(0,5)` function.   
>   
> Sure, in some situations I want very precises control over what engine and distribution objects are used, so I can make tradeoffs between performance, memory consumption and random number qualities/properties. But in many more scenarios I just want a random number with some reasonable tradeoffs (as a non-expert in the field, I can't really make a good decision anyway)  
  
That's called std::default_random_engine  
  
> and due to the relatively complex interface of Boost.Random (and its c++11 version), people are still using `rand` (in particular in tutorials) far too often.  
>   
  
The biggest problem with `rand` is that it uses global state.  
A Boost.Random version of it would not be any better.  
  
> So would you consider adding a few convenience functions like get_random_number to this library? I'd be willing to make an according PR, but only if there is a realistic chance this gets merged.    
>   
  
No.  
  
> One of the major design questions I've seen in the past was how to make this thread-safe (either use thread_local storage or a mutex. I'd favor the use of a mutex/spinlock and a fast generator, because thread_local storage is not available in some (embedded) environments.  
>   
> I think there were also some papers about this to be discussed on the c++ committee, but I don't know what became of them, but maybe the design could follow whatever was proposed there  
> ..  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2018-12-18 09:17:32 UTC  
> Updated at: 2018-12-18 09:18:46 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-448151181  

> That's called std::default_random_engine  
  
Which gives me bits, not a number.  
  
> The biggest problem with `rand` is that it uses global state. A Boost.Random version of it would not be any better.  
  
Well, I disagree. As long as it is threadsafe a global rng is just fine. Again, if you need precise control over the performance or you need a repeatable rn sequence you are not going to use it (and you won't use std::default_random_engine either.  
  
But rand also has the disadvantage that (at least on some platforms) the quality of the random numbers is very poor and generating anything but an integer between 0 and RAND_MAX becomes tricky. All this can easily be fixed by a proper get_random_number function.   
  
> No.  
  
Sorry to hear that.

---

## Comment 3

> Username: Mike-Devel  
> Created at: 2019-01-17 10:41:34 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-455125907  

So this is officially declined and should be closed?

---

## Comment 4

> Username: Mike-Devel  
> Created at: 2019-05-13 12:14:28 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-491797856  

I guess that means yes

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-05-22 01:17:10 UTC  
> Updated at: 2019-05-22 02:33:36 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494615082  

@Mike-Devel : The RDRAND instruction present on newer x86 CPUs does not require a seed or require global state. Hence the following implementation will do what you want:  
  
```cpp  
#include <iostream>  
#include <immintrin.h>  
  
unsigned get_random_number(unsigned min, unsigned max)  
{  
    unsigned p;  
    _rdrand32_step (&p);  
    return min + (p % (max - min));  
}  
  
int main()  
{  
    int i = 0;  
    while (i++ < 10) {  
        std::cout << get_random_number(20, 40) << "\t";  
    }  
    std::cout << get_random_number(20, 40) << "\n";  
    return 0;  
}  
```  
  
On my machine, I get a different sequence each time, which is expected, as the Intel docs say that `rdrand` periodically reseeds itself with an internal source of entropy (probably `rdseed`). It [appears](https://software.intel.com/en-us/articles/intel-digital-random-number-generator-drng-software-implementation-guide) that every core has its own `rdrand` circuit, and hence calls will be threadsafe. Presumably there will be no problem if two threads run on the same core since the timeslicing will serialize the instruction.  
  
Obviously there's a lot of error handling that would need to be done, including compile-time detection of target support for the instruction, and analogous instructions would need to be found on ARM and RISC, or some sort of graceful error handling would need to be done.  
  
Wouldn't be easy, but it's not an unreasonable request either, given that Intel has already provided us with the capability. I would personally like to see it done.

---

## Comment 6

> Username: kotika  
> Created at: 2019-05-22 09:15:15 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494722233  

> On May 22, 2019, at 04:17, Nick <notifications@github.com> wrote:  
> Wouldn't be easy, but it's not an unreasonable request either, given that Intel has already provided us with the capability.  
>   
Its been a few years and it is not catching on, for a reason. RDRAND is yet another case of well-meaning people introducing  
yet another bad idea to random-number generation.  I reread the the whitepaper from CRYPTOGRAPHY RESEARCH, INC.  
who were hired by Intel for an audit. The basic facts are not in dispute:   
  
1) The ES (hardware Entropy Source) is a dual differential jamb latch with feedback. (page 7)  
  
2) The deterministic RNG is AES in counter mode, and reseeds every 512 steps from the hardware. (page 11)   
  
3) The RNG supports clock gating to reduce power consumption.   
If no application requests entropy for a short time, the RNG will freeze its clock and stop the ES. (page 17)  
  
4) The final system is 20 times slower than Mersenne Twister. (Wikipedia, stackexchange)  
  
  
My commentary:  
1) This has always (50 years?) been known to be a problematic idea, maybe they managed to solve the practical problems maybe not,  
there is no way to check, actually. Moreover, even theoretically it was never shown  
to produce true randomness beyond handwaving arguments about   
this system always being in a kind of disequilibrium. Does disequilibrium result in randomness?  
  
2) If we trust the strength of AES to not to reveal the internal state between reseeds, then it is more secure to   
seed once from hardware or whatever other secure source, and use AES the whole time without reseeding until the next reboot.  
Says the whitepaper on page 17: "Under moderate load, its output should be information-theoretically random. Under heavy load, it should provide security equivalent to 128-bit AES..."  
  
3) You will not be generating crypto keys all the time, so the hardware will be starting from a cold state each time, with all attendant concerns   
about cold startup. (see the literature on /dev/random)  
  
4) If it is not suitable for secure applications, and various open-source projects have actually actively removed RDRAND from their   
most critical codepaths, then maybe it is still ok for non-cryptographical purposes? The answer is still no. For simulations  
you need speed and reproducibility, RDRAND gives neither.  
  
Kostas Savvidis  
  
=======================  
NCSR "Demokritos"   
http://mixmax.hepforge.org

---

## Comment 7

> Username: NAThompson  
> Created at: 2019-05-22 13:44:39 UTC  
> Updated at: 2019-05-22 15:58:23 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494808797  

Hi,  
  
A few comments:  
  
> Moreover, even theoretically it was never shown to produce true randomness beyond handwaving arguments about this system always being in a kind of disequilibrium.  
  
I'd like a peer-reviewed source for this. Presumably someone has already put the instruction through the NIST statistical tests of randomness, because it's easy to do, and if they saw something strange, they'd holler. In any case, Boost.Random doesn't have the obligation to prove that rdrand produces true randomness, just call it `boost::random::rdrand` and leave the user to decide on whether that's useful for their purposes.  
  
> If it is not suitable for secure applications, and various open-source projects have actually actively removed RDRAND from their most critical codepaths, then maybe it is still ok for non-cryptographical purposes? The answer is still no. For simulations you need speed and reproducibility, RDRAND gives neither.  
  
  
OpenSSL [does use rdrand](https://github.com/openssl/openssl/blob/3c120f91656f90a9a3b629bb6ea9d5a1f0d4d678/crypto/engine/eng_rdrand.c) (see line 25)  
  
The [standard library uses it as well](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/src/c%2B%2B11/random.cc) (see line 142).  
  
[Bruce Schneier](https://www.schneier.com/blog/archives/2007/11/the_strange_sto.html) says that only the the elliptic curve variant of the NIST 800-90 standard was backdoored, and RDRAND doesn't use elliptic curves.  
  
Your point about non-reproducibility is well-taken; it should be added to the docs if @Mike-Devel decides to move forward.  
  
The thoughput of the instruction has been improving monotonically with time; since the SO answers you reference were posted, the instruction has gotten [7x faster](http://uops.info/html-instr/RDRAND_R64.html)

---

## Comment 8

> Username: Mike-Devel  
> Created at: 2019-05-22 16:20:05 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494873637  

The reason for this issue is that: All I want is a random number!  
  
it doesn't need to be "true" / "cryptographically secure randomness", it doesn't need to provide optimal performance, it doesn't need to scale perfectly with x-cores, it doesn't have to have a minimal memory footprint ....   
  
All I want is a quick and simple to use interface (e.g. `int i = random_number<int>(-10,10);`). Maybe I'm doing some unit-testing, maybe some Monte-Carlo-Simulation, maybe I'm just implementing a proof of concept/mock for something that needs to get tuned later, maybe I'm writing a small game or maybe just a throw away example to demonstrate something.   
  
There are so many situations, where I just want to quickly get a random number, without first having to think about what the optimal algorithm is, how to properly initialize the random number engine and without going through all of the boilerplate necessary to properly get a random number in plain boost or c++11 `<random>` style.  
  
Can I write my own wrapper to do this? Sure, but can't something like this be put in a common library like boost that many projects already use and where experts can think about how to best implement that function depending on the platform it has to run on. Also, while a generic solution will obviously not be optimal for many cases, it will most likely be at least close to the pareto front because experts have thought about it, whereas my homegrown solution may not even be that.  
  
Sure, the strength of c++ (and also many boost libraries) is that you have full control and can very finely tune your code where necessary, but for large parts of a c++ - code base (and some code-bases in general), productivity and simplicity is often still more important than being optimal (obviously within reason).  
  
I find it sad that neither the standard library, nor boost provide a convenient function for something as simple as getting a random number - no wonder people consider c++ hard to learn.

---

## Comment 9

> Username: Mike-Devel  
> Created at: 2019-05-22 16:26:12 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494875977  

> Your point about non-reproducibility is well-taken; it should be added to the docs if @Mike-Devel decides to move forward.  
  
Steven Watanabe already said he won't accept something like that into Boost.Random, so I don't see a way to move forward with this (although I'm still thankful you pointed out this possibility).

---

## Comment 10

> Username: Mike-Devel  
> Created at: 2019-05-22 16:35:59 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494879674  

> 4) If it is not suitable for secure applications, and various open-source projects have actually actively removed RDRAND from their most critical codepaths, then maybe it is still ok for non-cryptographical purposes? The answer is still no. For simulations you need speed and reproducibility, RDRAND gives neither.  
  
I've written many simulations (some of them running for days), where the speed of the random number generator was absolutely no concern at all. Reproducability is a different matter, which may or may not be a concern. Often it is not necessary at all or it is enough (or even better) to just log what random number was actually used as an input. Again: If I have very specific requirements there is a whole host of libraries and algorithms to choose from and selecting the optimal one may be an elaborate task, but time well spent. However, there are also many situations, where I don't have those requirements (yet) and any time thinking about those things is just wasted.

---

## Comment 11

> Username: NAThompson  
> Created at: 2019-05-22 16:36:44 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494879896  

> it doesn't need to be "true" / "cryptographically secure randomness", it doesn't need to provide optimal performance, it doesn't need to scale perfectly with x-cores, it doesn't have to have a minimal memory footprint .... All I want is a quick and simple to use interface (e.g. int i = random_number<int>(-10,10);).   
  
But with `rdrand` we can have it all: Reasonable speed with high quality randomness (presumably of cryptographic quality, since OpenSSL uses it), and no complicated seeding.  
  
> Steven Watanabe already said he won't accept something like that into Boost.Random, so I don't see a way to move forward with this (although I'm still thankful you pointed out this possibility).  
  
I imagine @swatanabe was simply unaware that there was a hardware implementation that could make what you envisioned possible.

---

## Comment 12

> Username: Mike-Devel  
> Created at: 2019-05-22 16:45:56 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-494882876  

> But with rdrand we can have it all: Reasonable speed with high quality randomness (presumably of cryptographic quality, since OpenSSL uses it), and no complicated seeding.  
  
And I'd gladly take it. Something like rdrand also seems to be the natural choice for me (either that or fallback to std::default_random_engine), but I'm (obviously) not an expert on this. My post was an answer to general "This won't be optimal/secure" response.

---

## Comment 13

> Username: kotika  
> Created at: 2019-05-22 23:15:37 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495008641  

> On May 22, 2019, at 19:36, Nick <notifications@github.com> wrote:  
>   
> Reasonable speed with high quality randomness (presumably of cryptographic quality, since OpenSSL uses it)  
  
======================================================================================  
Beginning with the 1.0.1f release of OpenSSL the RDRAND engine is no longer loaded by default* <https://software.intel.com/en-us/blogs/2014/10/03/changes-to-rdrand-integration-in-openssl#star1>.  
The impact of this from the users' and developers' perspectives is that, for the near future, random numbers obtained from the RAND_bytes() function will come from OpenSSL's software-based PRNG rather than directly from the RDRAND instruction.   
  
This change was made in part due to concerns in the user community around OpenSSL's reliance on a single entropy source for random number generation.  Essentially, OpenSSL did not want to force end developers and users onto a single entropy source without them being aware that it was happening.  
  
Unfortunately, this change also means that RDRAND is no longer OpenSSL's random number generator by default.  
  
https://software.intel.com/en-us/blogs/2014/10/03/changes-to-rdrand-integration-in-openssl  
======================================================================================

---

## Comment 14

> Username: NAThompson  
> Created at: 2019-05-23 00:39:36 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495024578  

And the [very next paragraph](https://software.intel.com/en-us/blogs/2014/10/03/changes-to-rdrand-integration-in-openssl) of your link says OpenSSL still uses it:  
  
> RDRAND still has a place in OpenSSL's future, however. In upcoming 1.0.2 releases, the PRNG within OpenSSL will actually call upon both the RDRAND and RDSEED instructions (if available) to augment its output. It will do this in two ways, by: XOR'ing random values obtained from the software PRNG with values obtained from RDRAND (in non-FIPS modes) Using RDSEED or RDRAND as an entropy source to seed the software PRNG (in both FIPS and non-FIPS modes) This new behavior of the built-in PRNG has been committed into the 1.0.2-stable branch of the OpenSSL source tree.  
  
Note that your link doesn't claim that RDRAND isn't a good rng, it claims that people want two sources of entropy, presumably as a form of defense in depth. That's good! But it really doesn't have relevance to whether or not boost should expose a `boost::random::rdrand()` call.

---

## Comment 15

> Username: swatanabe  
> Created at: 2019-05-23 00:48:07 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495025980  

AMDG  
  
On 5/22/19 6:39 PM, Nick wrote:  
> <snip>That's good! But it really doesn't have relevance to whether or not boost should expose a `boost::random::rdrand()` call.   
>   
  
It definitely shouldn't.  There's no way I'm going to add  
an API for a specific hardware architecture.  
  
In Christ,  
Steven Watanabe

---

## Comment 16

> Username: NAThompson  
> Created at: 2019-05-23 01:28:09 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495032829  

>  There's no way I'm going to add an API for a specific hardware architecture.   
  
Before we got distracted onto the question of whether `rdrand` was any good, I was going to point out that the desired API is possible on Power9 via `__builtin_darn`, and AMD via `rdrand` (of course), and Raspberry Pi via `/dev/hwrng`.

---

## Comment 17

> Username: kotika  
> Created at: 2019-05-23 11:00:33 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495171988  

> On May 23, 2019, at 04:28, Nick <notifications@github.com> wrote:  
> Before we got distracted onto the question of whether rdrand was any good, I was going to point out that the desired API is possible on Power9 via __builtin_darn, and AMD via rdrand (of course), and Raspberry Pi via /dev/hwrng.  
>   
> On May 22, 2019, at 19:20, Mike-Devel <notifications@github.com> wrote:  
> The reason for this issue is that: All I want is a random number!  
>   
Nick and Mike-Devel made a point about the unnecessary complexity of initializing an RNG and allocating a distribution.  
This is a valid concern of many users who are totally puzzled out by the bewildering stuff that made it into C++11. I am thinking about ways to give users a simpler interface and may at some point in the future make some proposal.  
The difficulty is  - as soon as you want to get random numbers you need to tell us what distribution you want,  
real or int, and range and what distribution, uniform, gaussian or poisson or one of a dozen others which are absolutely needed.  
Plus there are good technical reasons to keep it centralized - we dont want each RNG  
to duplicate work by rolling his/her own statistical distributions, esp as these are difficult to implement correctly for a non expert.  
  
Maybe something like this would be perceived as more convenient?   
  
rng_engine<flat> g;  
double z=g();                                  // output a uniform real number on [0,1]   
  
rng_engine<gaussian> g;  
double z=g();                                  // output a normally distributed real  
  
rng_engine<discrete,-10,10> g;  
int i=g();                                          // output an integer between [-10,10]  
  
(I am not saying this would be any good -  it is a pretty terrible idea to overload the operator() - right now it is reserved for ints between min and max, internal to each generator, plus I personally dont want to struggle with partial and vararg templates)  
  
Now, some people need a different "random"  
output from the program every time it runs. In the case of the casual user this is probably   
a misunderstanding of what the word "random" means, for serious MonteCarlo strict reproducibility definitely is a must.  
Still, there are use cases for "random" initialization, like games and crypto.   
My feeling is that it is hard to get this right, the crazy mess that is std::random_device is a testament to that.   
  
By the way, you should be aware that Intel makes a non-blocking guarantee for this instruction.  
 Sounds good, but it means that if internal hw tests fail, it will return after some unspecified time interval, but  
not return all zeros. Indication of failure is the carry flag. I dont see any such check for the carry flag in stdlib or openssl code.  
  
Cheers, Kostas

---

## Comment 18

> Username: NAThompson  
> Created at: 2019-05-23 12:30:15 UTC  
> Url: https://github.com/boostorg/random/issues/50#issuecomment-495198310  

> Indication of failure is the carry flag. I dont see any such check for the carry flag in stdlib or openssl code.  
  
Yeah I was wondering about that as well. But as you can see in my example, there's actually a return status to the intrinsic which I'm not checking. We could easily check it.  
  
> Maybe something like this would be perceived as more convenient?  
  
I think you are clearly on the right track here-once it collides with the real world it would clarify your thinking about this. But just as a draft I like it.
