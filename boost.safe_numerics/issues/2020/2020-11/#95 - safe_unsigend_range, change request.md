# #95 - safe_unsigend_range, change request [Closed]

> Username: GregKon  
> Created at: 2020-11-29 16:12:21 UTC  
> Updated at: 2021-05-23 22:11:31 UTC  
> Closed at: 2021-05-23 22:11:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95  

Dear Robert  
  
I think something may improve significantly library, consider scenario:  
  
```  
 using range_0_100 =  safe_unsigned_range<0, 100>;  
 void fun(range_0_100 var) {  
   auto var2 = var; //EXCEPT EXCEPTION 2  
  
   if (var2 > 100) { //EXCEPT EXCEPTION 3  
     std::cout << "contract broken.." << var << "\n";  
   }  
 }  
  
   range_0_100 test1 = 7;  
   uint8_t &ref = reinterpret_cast<uint8_t &>(test1);  
   ref = 200;   
     /* 2 lines above simulate:   
                                             1. buffer overflow  
                                             2. EMI effect  
                                             3. any issue with CPU or memory  
                                             4. stack overflow (possible on uC)  
                                             5. ugly casting as above  
                                             6. intentional security attack using hardware technique  
                                             7. more.. */   
   
     fun(test1); //EXCEPT EXCEPTION 1  
```  
When I use gcc 9.2.0 (mingw, windows) I do not have exception. since I would like to expect 1 to 3 possible points of exception.  
Contract in function fun() is broken, unfortunately assertions is still necessary...  
  
Such situation is obviously outside of abstraction (C++ or programming) but it really "real" in my work.   
If that doesn't convince you I may write a little bit more about possibly cause of failure that test1 will be not in range. Consider just power glitch on even good RAM memory.  
  
What you think?  
Is i possible test range not only during "write to" but also "read from" variable?

---

## Comment 1

> Username: robertramey  
> Created at: 2020-12-16 18:39:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-746799696  

hmmm - I'm not sure I'm seeing the same thing you are.  
  
var is guaranteed to be a value in the interval [0,100].  No exception possible.  
  
var2 is of type range_0_100.  Since it's initialized from var it is also guaranteed to be in the interval [0,100]. No exception possible.  
  
since var2 is guaranteed to be in [0,100], var > 100 is guaranteed to be false and the error message will never be displayed. I would expect most C++ compilers would optimize this out entirely.  
  
replace uint8_t &ref = reinterpret_cast<uint8_t &>(test1);  
with uint8_t ref = test1  // no exception is this case since test1 == 7  
  
ref = 200; // no exception since it's not a safe integer  
  
I don't really understand about the simulation argument.  
  
I seems to me that this whole example could be shortened to:  
`   
using range_0_100 =  safe_unsigned_range<0, 100>;  
void fun(range_0_100 var) {}  
  
range_0_100 ref = 200;  
     /* 2 lines above simulate:   
                                             1. buffer overflow  
                                             2. EMI effect  
                                             3. any issue with CPU or memory  
                                             4. stack overflow (possible on uC)  
                                             5. ugly casting as above  
                                             6. intentional security attack using hardware technique  
                                             7. more.. */   
   
     fun(ref);  
`

---

## Comment 2

> Username: GregKon  
> Created at: 2020-12-19 14:51:20 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-748484392  

Dear Robert  
  
I think it is misunderstand. Let me try explain.  
First of all I agree that "var is guaranteed" and so on.. absolutely, no argue with that!   
But this **guaranteed on C++ abstraction level** only, in such case it is always good, but code need to run on real machine.  
  
The simulation it is about for example: single RAM corruption (what is many often the cause of "blue screen" on PC) or break contract by client (for some reason, client/user does do this). I do not know what else I might present regarding 'simulation'.   
I think this is a background of profession, or maybe imagine this: computer in spaceship where cosmic radiation is quite high and risk or RAM corruption is significantly higher, the risk has to be addressed somehow.  
  
I have attached ready to compile example: library "fun(range_0_100 var)" and client used it.  
Client and library placed in different compiling units, so it is simulating real use of library.  
Tested on Visual Studio 17 and gcc 9.2.0. 2 different optimization level: no optimization and full optimization for performance. Neither of them does do optimization what you mention and always results is:   
`contract broken, var: 200`  
  
[ram_corruption_example.zip](https://github.com/boostorg/safe_numerics/files/5719524/ram_corruption_example.zip)  
  
My background is not common PC applications, where if something went wrong no one get hurt. However imagine PC application where you calculate "big currency exchange", and if overflow... you are of course on right side when using safe numeric but only in abstraction level.  
  
**SUMMARY:**  
I suppose it is question of "safe numeric" ' philosophy: shall it be addressed inside library (if possible) or shall we use extra assert() on passed arguments to library?  
  
/Greg

---

## Comment 3

> Username: robertramey  
> Created at: 2020-12-19 17:54:48 UTC  
> Updated at: 2020-12-19 18:13:19 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-748504636  

>But this guaranteed on C++ abstraction level only,  
  
Correct.  
  
> The simulation it is about for example: single RAM corruption  
  
OK  
  
The library is based on the idea that computing is a group o  abstract models.  Each abstraction is built on top of another extraction forming a tower of layers.  Each models presumes that the layers below it function as defined.  On this basis, the highest layer can be proved correct.  Highest layer is the program, lowest layer is machine hardware.   So safe numerics as currently defined and implemented cannot actually prove program correctness in the absence that lower level abstraction - machine hardware can be proved to never deliver a false result.  
  
Current layers:  
  
a) Mathematical algebra - depends on arithmetical operations in a world where things like infinities exist.  That is - not the real world.  Most programs are concieved with this model.  The fact that the mathematical world is not a perfect match for real world arithmetic is just ignored.  
b) C++ numerics - attempts to map mathematical algebra to hardware.  In cases where this cannot be done - it's just ignored and considered undefined behavior  
c) hardware - is presumed to accurately implement C++ numerics.  Cases where it fails to do so are just ignored.  
  
To get a provably correct program one needs to be able to prove all operations in layers a), b) and c).  a) is the realm the proving an algorithm in mathematics.  b) proves the operations required to implement a) above can never deliver a wrong answer.  This is actually implemented in two sub layers.  checked<int>... - which replaces mathematical numerics with numeric types modeling finite mathematical groups.  operations on these objects can be proved to never fail.  The layer above this maps checked<...> to C++ arithmetic (making types the same via type promotion, etc.).  To prove this requires the addition of exceptions, error detection, etc.  
  
So hardware anomalies are ignored.  
  
So we're missing one more layer - a hardware level abstraction which can never fail.  This couple perhaps be implemented via other hardware (error correction registers or ?) or perhaps an interpreter which does something like this in software.  Note that this is totally orthogonal to the layers above it.  It really is doable.  There is academic research in this area.Your job, should you decide to accept it, it to implement this layer.  Doing this will make safe numerics that much more valuable so I'm looking forward to this.  Good Luck.

---

## Comment 4

> Username: GregKon  
> Created at: 2020-12-19 20:33:30 UTC  
> Updated at: 2020-12-19 20:33:47 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-748521633  

Indeed, there is hardware with ECC.  
I am too practical to play with hardware abstract and what I need it just a little bit more tests what are equivalent of standard asset(). that's all.  
  
In this case it is enough to implement user defined copy constructor.  
I will try to do this, it may be a little bit challenging since there is a lot of template magic.  
Lets this issue open for a while, I hope to receive some help in case of problem with this task.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-12-20 18:17:04 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-748642016  

Fine.  I think the really valuable part of this the exposition on how integer arithmetic is conceived as a layered set of abstractions from the real world problem down to the hardware.  Each layer is defined in terms of the layer immediately below it.  Each layer is proved correct based on the assumption that the layer below it can never fail. (this is lost in current systems).  This will prove that the whole system can never fail.  
  
The whole edifice depends on this layered structure.  Soooo don't reach from one layer to another - for example, don't try to detect hardware failures from a higher layer.  Such a task is too complex to be verified.  
  
To me, your concerns could be summarized as: OK we can prove the program never fails, if and only if the underlying hardware never fails.  My response is then - OK make hardware which can never silently fail.   Now the bottom layer is on the same level of correctness as all the other layers.  And the whole system cannot silently produce a false result.  Note that this is not the same as saying the program will always function correctly.  I believe that we can prove that it is impossible to write such a program in general - which is why the study of program correctness stalled after the work of Dikstra and Hoare in the 80's.  We can make progress because we now realized that "guaranteed program correctness" is unnecessarily strong condition.  We can do very good work with a weaker condition: "guaranteed no undetected failure" - which is what we have done.  
  
This whole discussion motivates me to learn something about "failsafe" hardware.  This would include systems with ecc built-in and systems built with parallel running processors which constantly compare state.  Very rich subject.  But first we have to convince the world that it needs safe numerics - just ignoring faults is not good enough.

---

## Comment 6

> Username: GregKon  
> Created at: 2020-12-29 09:55:11 UTC  
> Updated at: 2021-01-21 09:09:08 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/95#issuecomment-752017559  

since you are educating in this direction, I do also highly recommend glitching attack (security issue), for example start here: https://www.slideshare.net/ZiyadAlshehri/fault-injection-attacks  
  
I will back to this topic next year..  
happy new year!  
  
[update 2021-01-21] lets keep it open, I hope back to this in nest 2-3 weeks.
