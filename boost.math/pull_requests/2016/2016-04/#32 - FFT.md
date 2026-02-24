# #32 FFT [Closed]

> Username: jeremy-murphy  
> Created at: 2016-04-09 14:55:38 UTC  
> Updated at: 2017-11-06 14:41:02 UTC  
> Closed at: 2017-11-06 14:41:02 UTC  
> Url: https://github.com/boostorg/math/pull/32  

**Work-in-progress: do not merge.**  
  
Now I got a bit distracted from gcd and started to look at polynomial multiplication again.  
  
So here are two textbook algorithms for FFT, the textbook in this case being Introduction to Algorithms (2009) by Cormen et al.  
  
My plan is to use the GNU Scientific Library as a benchmark for comparison. From cursory observations it looks OK so far.  
  
Maybe important thoughts at the moment are what kind of interface would be ideal?

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2016-04-10 08:45:55 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-207946733  

[GSL](https://www.gnu.org/software/gsl/manual/html_node/Mathematical-Definitions.html#Mathematical-Definitions) briefly discusses whether to use +/- or -/+ exponent in the forward/inverse pair of FFT. Cormen and Numerical Recipes use +/-; GSL and FFTPACK use -/+.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2016-04-10 18:12:15 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208034553  

Getting a correct interface is going to be tricky IMO, the main issue for me is that there is no check (or possibility of one) for correct output array size.  One option would be to make the output type a Container, rather than an iterator.  But I'm not sure if that would restrict use cases?  
  
I'm also unsure at present, how this will be used with say integer types where std::complex is unavailable.  
  
Best, John.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2016-04-11 01:15:52 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208106570  

Another option is to operate in-place, as GSL does. Might seem weird when converting to/from reals but maybe not.  
  
Hmm, might just be that the user has to convert their data to floating point?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2016-04-11 12:15:21 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208312464  

On 11/04/2016 02:15, Jeremy W. Murphy wrote:  
  
> Another option is to operate in-place, as GSL does. Might seem weird   
> when converting to/from reals but maybe not.  
>   
> Hmm, might just be that the user has to convert their data to floating   
> point?  
  
If the goal is to use FFT acceleration of polynomial multiplication,   
then we can't use an inherently lossy format (floating point) to arrive   
at an exact integer result?  
  
BTW std::complex is restricted to just float, double and long double,   
nothing "longer".

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2016-04-11 12:20:01 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208313494  

Right. I seem to recall something in Knuth about numeric precision in this  
context but I'll have track it down.  
On 11 Apr 2016 10:15 PM, "jzmaddock" notifications@github.com wrote:  
  
On 11/04/2016 02:15, Jeremy W. Murphy wrote:  
  
> Another option is to operate in-place, as GSL does. Might seem weird  
> when converting to/from reals but maybe not.  
>   
> Hmm, might just be that the user has to convert their data to floating  
> point?  
  
If the goal is to use FFT acceleration of polynomial multiplication,  
then we can't use an inherently lossy format (floating point) to arrive  
at an exact integer result?  
  
BTW std::complex is restricted to just float, double and long double,  
nothing "longer".  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly or view it on GitHub  
https://github.com/boostorg/math/pull/32#issuecomment-208312464

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2016-04-11 12:35:02 UTC  
> Updated_at: 2016-04-12 19:48:11 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208318632  

Some multiple precision libraries split 32-bit integer values into 16-bit integer values.  
The FFT is carried out with double precision floating point. The 16-bit integer  
values are converted back to 32-bit integer values when handling the carries  
after the convolution and reverse transformation. This seems to preserve  
precision for up to a few million points in the FFT.  
  
I have recently written an extended complex class designed to handle  
user-defined-types in addition to boult-in float, double and long double.  
  
Another author was interested in getting an extended complex class  
into boost, but we never reached the review process.  
  
Regards, Chris  
  
> P. 307 in volume 2: "By a stroke of good luck, it turns out that everything  
> will work properly if we do the calculations with only a modest amount of  
> precision."  
> Of course there is more to it than good luck but the sentiment is  
> reassuring.  
> On 11 Apr 2016 10:19 PM, "Jeremy Murphy" jeremy.william.murphy@gmail.com  
> wrote:  
>   
> > Right. I seem to recall something in Knuth about numeric precision in this  
> > context but I'll have track it down.  
> > On 11 Apr 2016 10:15 PM, "jzmaddock" notifications@github.com wrote:

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2016-04-11 14:42:04 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208379923  

There are also areas of research on [integer FFT](http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=984749&queryText=integer%20Fast%20Fourier%20transform&refinements=4294944462) and of course a zillion articles on how to make it [fast](http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6021384&queryText=speeding%20up%20Fast%20Fourier%20transform&newsearch=true).

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2016-04-11 17:36:01 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208466449  

On 11/04/2016 15:42, Jeremy W. Murphy wrote:  
  
> There are also areas of research on integer FFT   
> http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=984749&queryText=integer%20Fast%20Fourier%20transform&refinements=4294944462   
> and of course a zillion articles on how to make it fast   
> http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6021384&queryText=speeding%20up%20Fast%20Fourier%20transform&newsearch=true.  
  
I confess this is an area I know very little about, I do notice GMP   
doesn't use floating point FFT's precisely because the last bit may be   
off:   
https://gmplib.org/manual/Other-Multiplication.html#Other-Multiplication

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2016-04-11 18:09:03 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208481541  

On 11/04/2016 15:42, Jeremy W. Murphy wrote:  
  
> There are also areas of research on integer FFT   
> http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=984749&queryText=integer%20Fast%20Fourier%20transform&refinements=4294944462   
> and of course a zillion articles on how to make it fast   
> http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6021384&queryText=speeding%20up%20Fast%20Fourier%20transform&newsearch=true.  
  
Forgot to say: Karatsuba may actually be of more interest, since it's   
advantages kick in much sooner?

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2016-04-12 02:16:23 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-208665369  

Hmm, I thought what they're saying there is that they don't use the special  
support in certain processors? If you go one level up in the documentation,  
they do indeed use FFT for the largest-size problems.  
  
I did browse those pages in the past, it's good to revisit them. Presumably  
one would ultimately want to adopt the same strategy of size-appropriate  
algorithms? Given the amount of research that goes into improving FFT and  
the general usefulness of it, it just seemed like a good starting point.  
Karatsuba is elegantly simple, so yeah, it's a good choice too.  
  
On 12 April 2016 at 04:09, jzmaddock notifications@github.com wrote:  
  
> On 11/04/2016 15:42, Jeremy W. Murphy wrote:  
>   
> > There are also areas of research on integer FFT  
> > <  
> > http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=984749&queryText=integer%20Fast%20Fourier%20transform&refinements=4294944462>  
> >   
> > and of course a zillion articles on how to make it fast  
> > <  
> > http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=6021384&queryText=speeding%20up%20Fast%20Fourier%20transform&newsearch=true  
> > .  
>   
> Forgot to say: Karatsuba may actually be of more interest, since it's  
> advantages kick in much sooner?  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/32#issuecomment-208481541

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2016-04-12 19:52:40 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-209077151  

Sorry, I think I just might have over-written some comments by mistake. Sorry.  
  
Some multiple precision libraries split 32-bit integer values into 16-bit integer values.  
The FFT is carried out with double precision floating point. The 16-bit integer values  
are converted back to 32-bit integer values when handling the carries after the  
convolution and reverse transformation. This seems to preserve precision  
for up to a few million points in the FFT.  
  
I have recently written an extended complex class designed to handle  
user-defined-types in addition to boult-in float, double and long double.  
It has been through some basic tests with multiprecision and fixed-point.  
  
Another potential Boost author was interested in getting an extended complex  
class into boost a few years ago, but we never reached the review process.  
  
Regards, Chris

---

## Comment 12

> Username: jeremy-murphy  
> Created_at: 2016-04-13 02:11:20 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-209194125  

Hi Chris,  
Thanks for pointing that out. Yeah, Knuth goes into some detail on choosing  
a decomposition of large numbers with sufficient precision to avoid  
numerical error.  
This implementation based on Cormen et al is probably too naive and I need  
to take into account Knuth's formulas.  
On 13 Apr 2016 5:52 AM, "Christopher Kormanyos" notifications@github.com  
wrote:  
  
> Sorry, I think I just might have over-written some comments by mistake.  
> Sorry.  
>   
> Some multiple precision libraries split 32-bit integer values into 16-bit  
> integer values.  
> The FFT is carried out with double precision floating point. The 16-bit  
> integer values  
> are converted back to 32-bit integer values when handling the carries  
> after the  
> convolution and reverse transformation. This seems to preserve precision  
> for up to a few million points in the FFT.  
>   
> I have recently written an extended complex class designed to handle  
> user-defined-types in addition to boult-in float, double and long double.  
> It has been through some basic tests with multiprecision and fixed-point.  
>   
> Another potential Boost author was interested in getting an extended  
> complex  
> class into boost a few years ago, but we never reached the review process.  
>   
> Regards, Chris  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/math/pull/32#issuecomment-209077151

---

## Comment 13

> Username: jeremy-murphy  
> Created_at: 2017-11-06 14:41:02 UTC  
> Url: https://github.com/boostorg/math/pull/32#issuecomment-342168314  

I'm going to close the branches that I'm not actively working on so that they don't distract me and others -- anyone is welcome to pick up where I left off of course.

---
