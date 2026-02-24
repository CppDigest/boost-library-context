# #43 - Questions about uniform_int_distribution [Closed]

> Username: drauch  
> Created at: 2018-09-22 11:17:30 UTC  
> Updated at: 2018-09-23 09:20:46 UTC  
> Closed at: 2018-09-23 09:20:46 UTC  
> Url: https://github.com/boostorg/random/issues/43  

I'm currently trying to understand `boost::uniform_int_distribution` and I have problems to understand some code in the branch where the RNG's range (`brange`) is smaller than the target range (`range`).  
  
I understand the code up to the end of the while loop. Then we need to generate the last `result_increment`:  
  
* Why the check in line 171 for overflow? `result_increment` has a maximum of `range/mult`, so multiplying it with `mult` will give a maximum of `range`. Why do we need to perform this check?  
  
Thank you in advance for your time!

---

## Comment 1

> Username: drauch  
> Created at: 2018-09-22 12:01:39 UTC  
> Url: https://github.com/boostorg/random/issues/43#issuecomment-423739354  

Also, as a performance improvement, it looks like the calculation of `limit` could be moved out of the for loop.

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-09-22 15:42:21 UTC  
> Url: https://github.com/boostorg/random/issues/43#issuecomment-423752814  

AMDG  
  
On 09/22/2018 05:17 AM, drauch wrote:  
> I'm currently trying to understand `boost::uniform_int_distribution` and I have problems to understand some code in the branch where the RNG's range (`brange`) is smaller than the target range (`range`).  
>   
> I understand the code up to the end of the while loop. Then we need to generate the last `result_increment`:  
>   
> * Line 169: Is `range/mult` always 1? When is it possible that it is not 1?  
  
The upper limit is brange.  
For example, if brange = 3, range = 62,  
then at this point, mult = 16, range/mult = 3, result \in [0, 16)  
  
> * Why the check in line 171 for overflow? `result_increment` has a maximum of `range/mult`, so multiplying it with `mult` will give a maximum of `range`. Why do we need to perform this check?  
>   
  
I think you're right, but I'm not inclined to  
touch this code, since it's proven to be pretty  
fragile and it works correctly as is.  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-09-22 15:46:20 UTC  
> Url: https://github.com/boostorg/random/issues/43#issuecomment-423753079  

AMDG  
  
On 09/22/2018 06:01 AM, drauch wrote:  
> Also, as a performance improvement, it looks like the calculation of `limit` could be moved out of the for loop.  
>   
  
It shouldn't make much of a difference.  The expected  
number of iterations of the loop depends on the parameters,  
but should always be less than 2.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: drauch  
> Created at: 2018-09-22 15:50:04 UTC  
> Url: https://github.com/boostorg/random/issues/43#issuecomment-423753274  

Thank you again for your answers.  
  
> I think you're right, but I'm not inclined to touch this code, since it's proven to be pretty fragile and it works correctly as is.  
  
Did you initially write the code? Where did you learn how to do the calculations right? Did you make it up all by yourself? Are there any good articles or papers on the topic? I'd be very interested if you have any pointers :-)

---

## Comment 5

> Username: swatanabe  
> Created at: 2018-09-22 16:10:21 UTC  
> Url: https://github.com/boostorg/random/issues/43#issuecomment-423754720  

AMDG  
  
On 09/22/2018 09:50 AM, drauch wrote:  
> Thank you again for your answers.  
>   
>> I think you're right, but I'm not inclined to touch this code, since it's proven to be pretty fragile and it works correctly as is.  
>   
> Did you initially write the code? Where did you learn how to do the calculations right? Did you make it up all by yourself? Are there any good articles or papers on the topic? I'd be very interested if you have any pointers :-)  
>   
  
The original code was written by Jens Maurer.  I just  
fixed all the problems with integer overflow.  
  
The algorithm is essentially a base conversion,  
u() + u() * b + u() * b^2 + ... + u()*b^n  
  
followed by the standard rejection step just like  
in the branch where range < brange.  
  
In Christ,  
Steven Watanabe
