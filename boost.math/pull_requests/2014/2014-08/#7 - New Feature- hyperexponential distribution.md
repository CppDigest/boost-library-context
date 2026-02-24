# #7 New Feature: hyperexponential distribution [Merged]

> Username: sguazt  
> Created at: 2014-08-22 16:43:36 UTC  
> Updated at: 2014-10-12 16:33:51 UTC  
> Merged at: 2014-10-12 16:33:51 UTC  
> Closed at: 2014-10-12 16:33:51 UTC  
> Url: https://github.com/boostorg/math/pull/7  

The hyperexponential distribution (http://en.wikipedia.org/wiki/Hyperexponential_distribution) is a continuous probability distribution widely used in queueing theory (e.g., to model the service time of k parallel queueing stations, each having the service time distribution as an exponential distribution).  
  
It can also be used in distribution fitting, to approximate long-tail probability distributions.  
  
I've implemented it.  
Test results have been compared with Wolfram Mathematica 10, which has the HyperExponential distribution (http://reference.wolfram.com/language/ref/HyperexponentialDistribution.html).  
All tests have been compiled with GCC 4.8.3 on Linux Fedora 20 x86_64 (and on an Intel Core i7).  
  
Documentation is still incomplete (I need to document various functions).  
  
Please, can you consider to merge this request?  
  
Thanks,  
  Marco

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-23 10:44:18 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53149434  

Thanks for the submission, at first glance that looks very impressive!  I'll investigate in more detail later.

---

## Comment 2

> Username: sguazt  
> Created_at: 2014-08-24 12:49:02 UTC  
> Updated_at: 2014-08-24 12:49:23 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53191577  

Thank you John.  
I'm updating the doc.  
Do you know I can test it?  
I've tried with  
`$ cd boost/libs/math`  
`$ ../../b2 doc`  
but it seems no output is generated under the `math` directory.  
  
Thank you so much.  
  Marco

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-24 14:23:00 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53194861  

To rebuild the docs (which takes a while!) cd into libs/math/doc then invoke b2.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2014-08-24 14:31:00 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53195126  

Marco,  
  
I have some questions/comments for you:  
  
1) First off I think your code is more accurate than you give it credit: if I change the tolerance to 100 eps in the test program then all the tests still pass.  
  
2) Looking at this comment:  
  
```  
// We must use a low precision since it seems the involved computations are very challenging from the numerical point of view.  
// Indeed, both Octave 3.6.4, MATLAB 2012a and Mathematica 10 provides different results.  
// E.g.:  
//  x = [0 1 2 3 4]  
//  p = [0.2 0.3 0.5]  
//  r = [0.5 1.0 1.5]  
//  PDF(x)  
//    - MATLAB:      1.033333333333333,  0.335636985323608,  0.135792553231720,   0.061039382459897,   0.028790027125382  
//    - Octave:      1.0333333333333332, 0.3356369853236084, 0.1357925532317197,  0.0610393824598966,  0.0287900271253818  
//    - Mathematica: 1.15,               0.3383645184340184, 0.11472883036402601, 0.04558088392888389, 0.02088728412278129  
```  
  
I'm very surprised at how inaccurate the other packages are: your code agrees completely with Mathematica in this case, and looking at the rather simple arithmetic involved so it should!  Is there any chance you made a mistake feeding the starting values into those other packages?  Just checking!  
  
3) I'd like to change your test program to test float and long double as well (as the other distributions do), is it possible to persuade Mathematica to give you 35-decimal places in the expected results?  That would allow for testing 128-bit floating point types.  If you can generate a few more test cases too that would be great as well ;-)  I'd do this myself on WolframAlpha but it seems not to recognise this distribution...  
  
Thanks, John.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2014-08-24 17:46:08 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53201080  

Update: I've pulled this into the "hyper_exponential" feature branch (https://github.com/boostorg/math/tree/hyper_exponential), also made a few changes to suppress warnings and enable testing with more types.

---

## Comment 6

> Username: sguazt  
> Created_at: 2014-08-24 17:48:20 UTC  
> Updated_at: 2014-08-24 17:48:47 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53201141  

Hi John,  
  
_For comment no. 1:_  
  
You're right. Indeed the problem is more evident for the float type.  
With double, I can set a tolerance up to 2_10^3_eps.  
Instead, with float, if I simply use a tolerance of 10^-5 I get 9 errors from the test. For instance:  
`test/test_hyperexponential_dist.cpp(174): error in "ccdf": difference{1.07149e-05%} between boost::math::cdf(boost::math::complement(dist, static_cast<RealT>(2))){0.139070019} and static_cast<RealT>(0.13907000738920425){0.139070004} exceeds 9.99999975e-06%`  
  
In your case, does the 100*eps tolerance work for float?  
Anyway, I've rearranged the test unit so that I can specify a different tolerance according to the real type.  
  
_For comment no. 2:_  
  
To test under MATLAB/Octave, I've created the following two scripts:  
- hyperexppdf.m  
  
``` matlab  
function p = hyperexppdf(x, probs, rates)  
%HYPEREXPPDF Hyperexponential probability density function  
p = [];  
for i=1:length(x)  
    p = [p; probs'*exppdf(x(i), rates)];  
end  
```  
- hyperexpcdf.m  
  
``` matlab  
function [p] = hyperexpcdf(x, probs, rates)  
%HYPEREXPCDF Hyperexponential cumulative distribution function  
p = [];  
for i=1:length(x)  
    p = [p; probs'*expcdf(x(i), rates)];  
end  
```  
  
And used them inside MATLAB 2012a...  
  
``` matlab  
>> format long  
>> hyperexppdf([0 1 2 3], [.2 .3 .5]',[.5 1 1.5]')  
ans =  
   1.033333333333333  
   0.335636985323608  
   0.135792553231720  
   0.061039382459897  
>> hyperexpcdf([0 1 2 3], [.2 .3 .5]',[.5 1 1.5]')  
ans =  
   0  
   0.605860551484949  
   0.823937718193406  
   0.916900487436001  
```  
  
... and Octave 3.6.4  
  
``` matlab  
octave:1> format long  
octave:2> hyperexppdf([0 1 2 3], [.2 .3 .5]',[.5 1 1.5]')  
ans =  
   1.0333333333333332  
   0.3356369853236084  
   0.1357925532317197  
   0.0610393824598966  
octave:3> hyperexpcdf([0 1 2 3], [.2 .3 .5]',[.5 1 1.5]')  
ans =  
   0.000000000000000  
   0.605860551484949  
   0.823937718193406  
   0.916900487436001  
```  
  
If you have the opportunity to try to run these scripts, I'd be very curious about the results.  
  
_For comment no. 3:_  
I've changed the test unit to use the new output of Mathematica. I've redone computations by using `SetPrecision[<expression>, 35]`.  
Also, I've added test cases for `long double` and as tolerance I used 5_10^6_eps, which on my machine makes tests succeed.  
  
Cheers,  
  Marco

---

## Comment 7

> Username: sguazt  
> Created_at: 2014-08-24 17:55:39 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53201376  

John,  
I saw your message about the merge too late (I've just pushed some changes in the /old/ branch).  
Can I do changes in that new branch directly?  
  
Marco

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2014-08-24 18:41:04 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53202655  

No problem, I've manually merged them to my local copy, but when convenient, if you can switch to working on the new/modified version that would be great!  
  
In the mean time: those new test values don't look quite right to me, for example, given your test distribution, PDF at 0, should be exactly 1.15 I believe, it's exactly:  
  
2/10 \* 1/2 + 3/10 + 3/2 \* 1/2 = 1.15  
  
But your test value is 1.149999999999999911182158029987476766109466552734375L which clearly has garbage beyond double precision.  Maybe the input values are truncated to double before the calculation?  How about if you represent the inputs exactly as fractions (an alternative is to always pick inputs that are exactly representable in binary)?  
  
I'm not an Octave or Matlab user so I haven't tested your scripts, but I suspect they have an error somewhere, as in C++:  
  
```  
  double p[] = { 0.2, 0.3, 0.5 };  
  double r[] = { 0.5, 1.0, 1.5 };  
  
  for(unsigned i = 0; i < 5; ++i)  
  {  
     double res = 0;  
     for(unsigned j = 0; j < 3; ++j)  
        res += p[j] * pdf(exponential_distribution<double>(r[j]), i);  
     std::cout << std::setprecision(16) << res << " ";  
  }  
  std::cout << std::endl;  
```  
  
I get the same output as Mathematica.

---

## Comment 9

> Username: sguazt  
> Created_at: 2014-08-24 19:53:17 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53204810  

You're right, by using fractions Mathematica gives the right results.  
I'm going to update the test suite (in the new branch), tomorrow.  
Also, I will try if fractions make MATLAB/Octave work better.  
  
Thanks,  
  Marco

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2014-08-25 08:38:14 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53240253  

Thanks, also spotted that the quantile-from-complement test actually tests the ccdf not quantile.

---

## Comment 11

> Username: sguazt  
> Created_at: 2014-08-25 09:04:21 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53242363  

Hi John,  
  
I've changed the test unit with the new results and I've fixed the comment for testing quantile-from-complement in Mathematica.  
  
Also, I've changed `hyperexponential.hpp` file so that the `pdf` function uses the PDF of the `exponential` distribution (it forgot to restore it after I commented it for debugging purpose).  
  
Note, all changes have been done and pushed to the branch `feature/hyperexponential_dist` since I'm not allowed to write on the `origin/hyper_exponential` branch. So what I've done is:  
- Merged changes from `hyper_exponential` to `feature/hyperexponential_dist`  
- Committed on `feature/hyperexponential_dist`  
  
Hope this is OK  
  
Cheers,  
  Marco

---

## Comment 12

> Username: sguazt  
> Created_at: 2014-08-25 09:16:53 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53243434  

Ah, unfortunately the use of fractions did not improve MATLAB/Octave output (same results).

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2014-08-25 09:31:35 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53244671  

Thanks for the update - a few tweeks and all the tests pass at 80-bit long double precision on Mingw with 100eps tolerance.  Repo updated.  
  
With regard to your .m scripts, my suspicion is that they're not calculating what you think they are, but I don't know how to help you with those, sorry!  The calculation of the pdf is really a rather simple series (albeit each term is expensive to calculate), there's no way rounding error would leave you that far off IMO.

---

## Comment 14

> Username: sguazt  
> Created_at: 2014-08-25 09:40:11 UTC  
> Updated_at: 2014-08-25 09:41:03 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53245396  

Good.  
For the doc. I've tried to call b2 under math/doc. What I've got is:  
`- symlinks supported       : yes (cached)`  
`warn: Unable to construct ./standalone`  
`...patience...`  
`...patience...`  
`...found 3613 targets...`  
  
I suspect that the warning above is not just a warning since I don't see any PDF file.  
Since, I've never built Boost doc, can you address me to some help page (if any)?  
  
Thanks,  
  Marco

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2014-08-25 10:39:45 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53249945  

Unfortunately getting the doc build setup is rather tricky as quite a few tools are involved, most of it is documented here:  https://svn.boost.org/trac/boost/wiki/BoostDocs/GettingStarted  
  
You only abolutely need, xsltproc, the docbook XSL stylesheets, the Docbook 4.2 DTD, and then in your user-config.jam:  
  
using xsltproc ;  
using boostbook  
    : path-to-xsl-stykesheets  
    : path-to-dtd  
    ;  
using quickbook ;  
  
You can speed up the build a lot if you build release versions of the quickbook and autoindex tools and then change the quickbook line to:  
  
using quickbook : full-path-of-quickbook-executable ;  
  
and append:  
  
using auto-index : full-path-of-auto-index-executable ;  
  
Don't bother with PDF's yet, that opens a whole other can of worms, default build will produce HTML.

---

## Comment 16

> Username: sguazt  
> Created_at: 2014-08-25 12:34:32 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53258060  

Hi John,  
Sorry if I bother you, but I'm still unable to build the doc. If there is a more faster way to test the doc files I'm writing, please tell me :-)  
  
Anyway, I've done the following steps:  
1. Installed requirements: `xsltproc`, `docbook-dtds`, `docbook-xsl`  
2. Build quickbook:  
`cd tools/quickbook/`  
`../../bjam dist-bin`  
3. Build auto_index:  
`cd ../auto_index/build`  
`../../../bjam dist-bin`  
4. Created the `user-config.jam` file  
`cd ../../../libs/math/doc`  
`vi user-config.jam`  
  
```  
     using xsltproc ;  
     using boostbook  
      : /usr/share/sgml/docbook/xsl-stylesheets  
      : /usr/share/sgml/docbook/sgml-dtd-4.2  
      ;  
     using quickbook : /home/sguazt/sys/src/git/boost/dist/bin/quickbook ;  
     using auto-index : /home/sguazt/sys/src/git/boost/dist/bin/auto_index ;  
```  
1. Edit `math.qbk` to add:  
   `[def __hyperexponential_distrib [link math_toolkit.dist_ref.dists.hyperexponential_dist Hyperexponential Distribution]]`  
2. Edit `distributions/dist_reference.qbk` to add:  
   `[include hyperexponential.qbk]`  
3. Build the doc for `math`:  
   `../../../b2`  
  
I get:  
  
```  
    - symlinks supported       : yes (cached)  
warn: Unable to construct ./standalone  
...patience...  
...patience...  
...found 3614 targets..  
```  
  
How can I know if it worked?

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2014-08-25 16:12:59 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53285843  

Well I did warn you it was tricky to get working!  
  
What I should have said, was that your user-config.jam needs to go in your home directory, and should be based on boost-path/tools/build/example/user-config.jam with the content I suggested at the _end_ of the file.  You probably want to uncomment the GCC config too just to make life easier.  When it's working it'll print a message at the end saying how many targets it's updated, and there should be no "..failed to update..." messages.  It'll also take a while to run as the XSL transform is pretty slow.  
  
HTH, John.

---

## Comment 18

> Username: sguazt  
> Created_at: 2014-08-25 16:23:48 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53287254  

Thank you, John.  
It works!!  
  
Marco

---

## Comment 19

> Username: sguazt  
> Created_at: 2014-08-26 15:16:53 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53437414  

Hi John,  
I've (hopefully) finished the doc for hyperexponential.  
Please, take a look.  
  
What was very tricky is writing the doc (sometimes quickbook produces weird output) and producing all the graphics for equations (MathML+SVG).  
At last, since I'm more confortable with LaTeX notation, I've used MathJax to produce MathML from LaTeX, and then pMML2SVG (which seems less tricky than SVGMath) to convert to SVG.  
  
Also, for the plot, since I didn't find Boost.svg_plot, I created the SVG and PNG with another tool and put them inside the doc/graph directory. Anyway, I've changed graphs/dist_graphs.cpp as well (but not compiled).  
  
Cheers,  
 Marco

---

## Comment 20

> Username: pabristow  
> Created_at: 2014-08-26 16:41:09 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53450774  

Ok – I’m back from being away from home and I have this on my TODO list now.  
  
All looking promising.  
  
Don’t worry about the graphs and other doc things too much – I’ll sort them out.  
  
Cheers  
  
Paul  
  
---  
  
Paul A. Bristow  
  
Prizet Farmhouse  
  
Kendal UK LA8 8AB  
  
+44 (0) 1539 561830  
  
From: Marco Guazzone [mailto:notifications@github.com]   
Sent: 26 August 2014 16:17  
To: boostorg/math  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
Hi John,  
I've (hopefully) finished the doc for hyperexponential.  
Please, take a look.  
  
What was very tricky is writing the doc (sometimes quickbook produces weird output) and producing all the graphics for equations (MathML+SVG).  
At last, since I'm more confortable with LaTeX notation, I've used MathJax to produce MathML from LaTeX, and then pMML2SVG (which seems less tricky than SVGMath) to convert to SVG.  
  
Also, for the plot, since I didn't find Boost.svg_plot, I created the SVG and PNG with another tool and put them inside the doc/graph directory. Anyway, I've changed graphs/dist_graphs.cpp as well (but not compiled).  
  
Cheers,  
Marco  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/7#issuecomment-53437414 .  https://github.com/notifications/beacon/1076737__eyJzY29wZSI6Ik5ld3NpZXM6QmVhY29uIiwiZXhwaXJlcyI6MTcyNDY4NTQxMywiZGF0YSI6eyJpZCI6NDA0MjM2Nzh9fQ==--31e722aba5dd822336c87bb30b27cd5cfc9e6cf5.gif

---

## Comment 21

> Username: jzmaddock  
> Created_at: 2014-08-26 18:06:59 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53463716  

Thanks, I've pulled your changes into the Math lib hyper_exponential branch.  The main issue I notice is the size of png's (they're huge!), but we can hopefully fix that easily enough...  
  
I'll look at this again in a couple of days, John.

---

## Comment 22

> Username: sguazt  
> Created_at: 2014-08-26 19:34:54 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53476081  

Thank you, both.  
  
Also, I forgot to tell you that even if the HTML is correcly generated, the math doc generation gives me an error about an unknown reference ID.  
The 'offending' link is: `[link range.concepts Range]`  
My intent is to refer to the Boost.Range doc.  
To do so, I've put the `range.concepts` reference I've found in the Boost.Range quickbook doc. I didn't find a different way to refer to 'internal doc' and I don't know if it is the right way to do this.  
If not, please tell me how and I will fix the doc. :)  
  
For the PNG files, all the equation figures have been generated at 90dpi (I've used `rsvg-convert` from [librsvg2](https://wiki.gnome.org/action/show/Projects/LibRsvg?action=show&redirect=LibRsvg)). Instead, the plot figure has been generated directly from Mathematica by the `Export` function with default options (possibly, it allows to change the [ImageSize](http://reference.wolfram.com/language/ref/ImageSize.html).  
  
Cheers,  
  Marco

---

## Comment 23

> Username: sguazt  
> Created_at: 2014-08-28 08:18:13 UTC  
> Updated_at: 2014-08-28 12:09:23 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53686485  

Hello,  
I've just added some more constructors (for convenience):  
- default constructor  
- construction by (number of phases, rate vector) via iterator or range (the phase probabilities are computed as 1/<number of phases>).  
  
Also, I've added some more test cases for class constructions and accessors.  
In my opinion the implementation is complete. :)  
  
Cheers,  
Marco

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2014-08-28 12:37:17 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53713554  

Thanks Marco, I still haven't caught up, but from a design point of view, the new constructors don't look quite right, if we take for example:  
  
hyperexponential_distribution(std::size_t n, RateIterT rate_first, RateIterT rate_last)  
  
The parameter n is redundent as it is the same as std::distance(rate_last, rate_first)?  
  
The problem of course is that we can't simply remove that parameter as we already have a 2-arg constructor, it should be possible to select the correct constructor via SFINAE though based on the is_iterator trait currently being talked about on the ML or something similar.  
  
I'll experiment later (unless you beat me to it!)  
  
Cheers, John.

---

## Comment 25

> Username: sguazt  
> Created_at: 2014-08-28 12:55:15 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53715352  

Indeed, you're right.  
However, as you pointed out, I used the parameter `n` for disambiguation purpose.  
Unfortunately, I don't know too much about SFINAE (and, currently, I don't have too much time to study it) so I'm not able to implement your suggestion.  
  
To keep things simple, we can simply remove the two constructors taking the `n` parameter.  
As I said, they have been added only for convenience. The user can mimic them without efforts. For instance:  
  
``` c++  
    hyperexp_dist(n, rates)  
```  
  
=> (can be obtained as)  
  
``` c++  
   hyperexp_dist(std::vector<double>(n, 1.0/n), rates)  
```  
  
and  
  
``` c++  
    hyperexp_dist(n, rates.begin(), rates.end())  
```  
  
=>  
  
``` c++  
   const std::vector<double> probs(n, 1.0/n);  
   hyperexp_dist(probs.begin(), probs.end(), rates.begin(), rates.end());  
```  
  
Let me know.  
  
Cheers,  
Marco

---

## Comment 26

> Username: pabristow  
> Created_at: 2014-08-29 17:33:27 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53906623  

After more fumbling with GIT, I've finally got to glance at this, and make some only superficial comments.  
  
Impressive :-)  
  
Built the test OK  (Microsoft Visual C++ 2013) using VS IDE and ran with no errors.  
  
but got a blizzard of 4127 errors that I 'cured' by checking _MSC_VER rather than BOOST_MSVC right at the head of test  math\test\test_hyperexponential_dist.cpp  
  
#ifdef _MSC_VER  
  
# pragma warning(disable:4127) // conditional expression is constant  
  
#endif  
  
In the past I've found that BOOST_MSVC is not defined until after the first boost include - and that is often too late :-(  
  
Of course this isn't needed for using bjam to run the tests.  
  
I note with amusement that Marco can't spell percentage either ;-))  
(Or is this a copy'n'paste?)  
  
For future reference, we have started to use more digits from Wolfram in case we start using 256 floating-point with boost.Multiprecision or ?  50?  But don't change it now.  
  
I haven't rebuilt the docs, but Quickbook looks good, though I don't think the graph is ideal.  I'll look at this.  I also think that some working example of usage would be nice. It is more complicated that many other distributions -  especially with multiple possible constructors and vectors/iterators/ranges.    
  
 A practical example of use would be good as well.  The wikipedia article mentions about phones and internet (Anja Feldmann I, Ward Whitt)  but I'm sure there are others.  Do you have any ideas about this?  
  
Enough for now.  
  
Paul  
  
PS I'm working through the other distributions to permit 'the whole (positive) real line' including infinity, but this can be added later?

---

## Comment 27

> Username: sguazt  
> Created_at: 2014-08-29 19:42:30 UTC  
> Updated_at: 2014-08-29 19:43:04 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53921402  

Paul, thank you for the feedback.  
  
In the next days, I will try to extend the _Applications_ section of the doc and to add a _Example_ section as well.  
  
For the graph, what do you like to see? Do you think that plotting the comparison of the hyperexp with respect to an exp distribution having the same mean would be more useful? Indeed, a feature of hyperexp distribution is to have a Coefficient of Variation (CoV) > 1, while the exp distribution has a CoV = 1. Hence, the hyperexp distribution has a larger variance than the exp distribution.  
  
Thank you so much,  
  Marco  
  
PS: I didn't find the misspellings. Do you remember where they are, so that I can fix them?

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2014-08-30 16:14:18 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53962831  

Marco, Paul,   
  
I've updated the hyper_exponential branch to contain:  
- Regenerated versions of the PNG's - this was just a matter of running Marco's .mml files through our scripts - it just means they have the same dpi as the rest of the docs now.  
- SFINAE enabled versions of Marco's new constructors.  
  
All the tests pass with msvc, gcc and Intel, so like Marco, I think the implementation is done now.  
  
Now to the docs....  the main issue as I see it is explaining the constructors, the problem is that a formal specificatoin based on the actual definitions aren't very user friendly... in fact most users won't care about the details, they'll want it to "just work" based on a description of what you can construct the type from?  Basically you can:  
- Default construct.  
- Construct from a single range or initializer list if all the probabilities are equal.  
- Construct from two iterators if all the probabilities are equal.  
- Construct from two ranges or initializer lists.  
- Construct from 4 iterators.  
  
Shall I try to write something up or do you want to have a go?  
  
Paul: I also don't see much wrong with the graph?

---

## Comment 29

> Username: sguazt  
> Created_at: 2014-08-31 08:34:52 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53981319  

Thank you John, good job!  
Unfortunately, on my system (GCC 4.8.3+Linux x86_64) it doesn't compile:  
  https://gist.github.com/sguazt/a52df1c9dde04c132aa9  
  
Also, I have some comments/questions about the code:  
In constructor  
  
``` c++  
    public: template <typename RateIterT, typename RateIterT2>  
            hyperexponential_distribution(RateIterT const& rate_first, RateIterT2 const& rate_last,  
            typename boost::enable_if_c<boost::has_pre_increment<RateIterT>::value || boost::has_pre_increment<RateIterT2>::value>::type* = 0)  
```  
1. I think the OR `||` should be replaced with AND `&&`, do you?  
2. I think that the `RateIterT2` should be removed since (in addition to be redundant) the two parameters must have the same type (i.e. `RateIterT == RateIterT2`)  
  
Finally, in my opinion, things get very complicated from the user point of view. While these constructors add more flexibility, at the same time they are very hard  to understand and can be source of confusion (at least for users that don't know very much about meta-programming). Said this, I'd be inclined to remove them and leave only the following constructors:  
  
``` c++  
hyperexp_dist();  
hyperexp_dist(prob, rates);  
hyperexp_dist(probs.begin(), probs.end(), rates.begin(), rates.end());  
hyperexp_dist({{....}, {...}}); // Initializer lists  
```  
  
What do you think?

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2014-08-31 09:05:30 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53981891  

Ah, with regard to not compiling I left out an important detail: the implementation uncovered a bug in type_traits (has_pre_increment and friends didn't handle array types as arguments) which I've fixed in type_traits develop.  So you'll need to latest type_traits code to make it work.  
  
> I think the OR || should be replaced with AND &&, do you?  
> I think that the RateIterT2 should be removed since (in addition to be redundant)   
> the two parameters must have the same type (i.e. RateIterT == RateIterT2)  
  
One would imagine that would be the case, however there is a perfectly legitimate use case in the tests:  
  
double rates[] = { 1, 2, 3 };  
hyperexponential he(rates, rates+3);  
  
The problem here is that we're passing two different types to the constructor - an array type and a pointer.  In order to disambiguate all the possible constructor arguments (and believe me I tried quite a few permutations), the two enable/disable_if overloads basically need to be identical apart from the enable/disable_if condition.  Hense the comment: "Note that we allow different argument types here to allow for construction from an array plus a pointer into that array".  
  
Would it be easier to understand if there was a single 2-arg constructor which used tag dispatching to select the correct method internally:  
  
template <class A1, class A2>  
hyperexponential_distribution(const A1& a1, const A2& a2)  
{  
  typedef mpl::bool_<has_pre_increment<A1>::value || has_pre_increment<A2>::value> tag;  
   two_arg_construct_imp(a1, a2, tag());  
}  
  
?  
  
I believe there are some technical reasons why that's a less optimal, but perhaps easier to understand?  
  
Aside: I'm not sure that the average user needs to understand how the meta-programming works, as long as they understand how to use the class.

---

## Comment 31

> Username: sguazt  
> Created_at: 2014-08-31 09:29:35 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53982595  

For the type_traits patch, on what branch is it?  
I've checked out both master and develop branch of type_traits and they are already up to date.  
  
For the RateIterT2 parameter issue, this  is very interesting. I thought that `rates` and `rates+3` have the same type (e.g., `double*`). Indeed, if I try with a simple test case:  
  
``` c++  
#include <numeric>  
#include <iostream>  
  
template <typename T>  
double sum(T first, T last)  
{  
    return std::accumulate(first, last, 0);  
}  
  
int main()  
{  
    double rates[] = { 1, 2, 3 };  
    std::cout << sum(rates, rates+3) << std::endl;  
}  
```  
  
and compile it with:  
  
```  
g++ -Wall -Wextra -ansi -pedantic  
```  
  
where `-ansi` makes GCC compile for C++98 standard, I get no error and no warning.  
  
Just to know, is this a _bug_ of GCC?  
  
Cheers,  
  Marco

---

## Comment 32

> Username: jzmaddock  
> Created_at: 2014-08-31 10:03:30 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53983299  

> For the type_traits patch, on what branch is it?  
  
Sorry, my bad: I forgot to push! :-(  
Now done.  
  
> Just to know, is this a bug of GCC?  
  
No, it's a different case: the type of "rates" in your example is "double[3]", which is convertible to "double*".  Overload resolution succeeds in that case because there is only one overload to consider, our case is more like having:  
  
template <typename T>  
double sum(T const& first, T const& last)  
{  
   return std::accumulate(first, last, 0);  
}  
template <typename T, class U>  
void sum(T const& first, U const& last)  
{  
   std::cout << "Ooops" << std::endl;  
}  
  
int main()  
{  
    double rates[] = { 1, 2, 3 };  
    std::cout << sum(rates, rates+3) << std::endl;  
}  
  
Which prints "Ooops".  Now before you say it, you can fix that case by making the first overload:  
  
template <typename T>  
double sum(T first, T last)  
{  
   return std::accumulate(first, last, 0);  
}  
  
But that then does the wrong thing in the case that we're passing two different arrays:  
  
int main()  
{  
    double rates[] = { 1, 2, 3 };  
    double weights[] = { 1, 2, 3 };  
    std::cout << sum(weights, rates) << std::endl;  
}  
  
Which should call the second overload but calls the first instead.  In other words if we want array types to be found as template args some of the time, they must be found all of the time.

---

## Comment 33

> Username: pabristow  
> Created_at: 2014-08-31 12:14:51 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53986036  

1  Example - there is a very simple one at http://reference.wolfram.com/language/ref/HyperexponentialDistribution.html  
  
"Suppose a customer is buying an appliance and is choosing at random between an appliance with average lifetime of 10 years and an appliance with average lifetime of 12 years. Assuming the lifetime of   
this appliance follows an exponential distribution, find the lifetime distribution of the purchased appliance:  
  
Find the probability that the appliance will work for more than 15 years:"  
  
But perhaps you have other ideas?  
  
2  Graph - I felt there were too many lines too close.  But it's certainly OK.  
  
3 We certainly need examples (as snippets) of using all the constructor forms.

---

## Comment 34

> Username: jzmaddock  
> Created_at: 2014-08-31 13:58:24 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53988728  

> 1  Example - there is a very simple one at http://reference.wolfram.com/language/ref/HyperexponentialDistribution.html  
>   
> "Suppose a customer is buying an appliance and is choosing at random between an appliance with average lifetime of 10 years and an appliance with average lifetime of 12 years. Assuming the lifetime of  
> this appliance follows an exponential distribution, find the lifetime distribution of the purchased appliance:  
>   
> Find the probability that the appliance will work for more than 15 years:"  
>   
> But perhaps you have other ideas?  
>   
> 2  Graph - I felt there were too many lines too close.  But it's certainly OK.  
>   
> 3 We certainly need examples (as snippets) of using all the constructor forms.  
  
Nod. sure.  
  
Paul did you mean to mark this as closed?

---

## Comment 35

> Username: pabristow  
> Created_at: 2014-08-31 15:21:05 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53991019  

Ooops no – fumbling again.  
  
What is it about GIT and GIThub that I find so incomprehensible?  
  
How do I put this right?  
  
Sorry  
  
Paul  
  
From: jzmaddock [mailto:notifications@github.com]   
Sent: 31 August 2014 14:58  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
> 1 Example - there is a very simple one at http://reference.wolfram.com/language/ref/HyperexponentialDistribution.html  
>   
> "Suppose a customer is buying an appliance and is choosing at random between an appliance with average lifetime of 10 years and an appliance with average lifetime of 12 years. Assuming the lifetime of  
> this appliance follows an exponential distribution, find the lifetime distribution of the purchased appliance:  
>   
> Find the probability that the appliance will work for more than 15 years:"  
>   
> But perhaps you have other ideas?  
>   
> 2 Graph - I felt there were too many lines too close. But it's certainly OK.  
>   
> 3 We certainly need examples (as snippets) of using all the constructor forms.  
  
Nod. sure.  
  
Paul did you mean to mark this as closed?   
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/7#issuecomment-53988728 .  https://github.com/notifications/beacon/1076737__eyJzY29wZSI6Ik5ld3NpZXM6QmVhY29uIiwiZXhwaXJlcyI6MTcyNTExMjcwNiwiZGF0YSI6eyJpZCI6NDA0MjM2Nzh9fQ==--cf7c1efd56a8acf15d12775c9ba5a4a4a2082fea.gif

---

## Comment 36

> Username: jzmaddock  
> Created_at: 2014-08-31 15:27:03 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53991203  

Hit the button that say reopen ;-)  
  
Now done.

---

## Comment 37

> Username: sguazt  
> Created_at: 2014-08-31 15:52:49 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-53992043  

Hi, I'm back again.  
  
For the SFINAE part, John thank you for the clarification. I got it.  
  
For the application of the hyperexp distribution, I've started to work on an example about the time to serve of window operators at an airport. Anyway, the example from Mathematica is good too and since it is already done we can take it. I'll add in the doc (explicitly citing the Wolfram reference).  
Paul, thank you for pointing it out.  
  
Finally, for the usage example, I still have to think about.  
  
Cheers,  
Marco

---

## Comment 38

> Username: sguazt  
> Created_at: 2014-08-31 21:32:12 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54001989  

Hi,  
  
I've pushed some updates:  
- Doc:  
  - added _Examples_ section,  
  - expanded _Applications_ section,  
  - updated class synopsis and reference with recently added constructors,  
  - general review (e.g., added more references,...)  
- Code:  
  - added include file for macro `BOOST_NO_CXX11_HDR_INITIALIZER_LIST`  
  - removed `const &` from `std::initializer_list` parameters (as from (Stroustrup,TCPL4e), §17.3.4.2).  
  
Let me know your opinions.  
  
Cheers,  
  Marco

---

## Comment 39

> Username: sguazt  
> Created_at: 2014-09-01 07:52:44 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54031054  

Just another commit:  
- added `#include <iterator>` for `std::distance`  
- some aesthetic changes  
  
Marco

---

## Comment 40

> Username: pabristow  
> Created_at: 2014-09-01 09:49:11 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54040925  

All sounds good - but I don't see these changes on a recent boost hyperexponential branch pull   
  
Revision: 60a6730fe22824fcac2b7a9820a15d9fdaf385a2  
Author: jzmaddock jzmaddock@gmail.com  
  
What am I doing wrong now?  
(I note pull request   
"sguazt wants to merge 45 commits into boostorg:master from sguazt:feature/hyperexponential_dist "  
is to boost:master when I would expect boost:develop?).  
  
Have we checked what an assert for size mis-match will tell the user?  This is bound to be a popular mistake ;-)  
  
The Wolfram example is perhaps a bit too simple (but the others ref by Wikipedia a bit too complicated - and need raw data that I didn't see given?).  Ideas?  
  
Paul

---

## Comment 41

> Username: sguazt  
> Created_at: 2014-09-01 10:57:11 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54046507  

Hi Paul,  
  
What you're looking is the `hyperexponential` branch (created by John).  
Instead, my commits are in the `feature/hyperexponential_dist` branch (since I don't have write permission) and needs to be merged in that branch.  
  
For the "different size" error, every constructor calls the function `hyperexp_detail::check_dist` which, among the various checks, tests for size consistency and raises a policy-based domain error with the following message:  
  
``` c++  
   "The parameters \"probabilities\" and \"rates\" must have the same length, but their size differ by: %1%."  
```  
  
For the doc, take a look at the new `Applications` and `Examples` sections.  
  
Marco

---

## Comment 42

> Username: jzmaddock  
> Created_at: 2014-09-01 11:17:41 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54048104  

> All sounds good - but I don't see these changes on a recent boost hyperexponential branch pull  
>   
> Revision: 60a6730fe22824fcac2b7a9820a15d9fdaf385a2  
> Author: jzmaddock jzmaddock@gmail.com  
>   
> What am I doing wrong now?  
  
It hasn't been merged yet - now done.  
  
John.

---

## Comment 43

> Username: pabristow  
> Created_at: 2014-09-01 13:12:47 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54057495  

From: jzmaddock [mailto:notifications@github.com]   
Sent: 01 September 2014 12:18  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
> All sounds good - but I don't see these changes on a recent boost hyperexponential branch pull  
>   
> Revision: 60a6730fe22824fcac2b7a9820a15d9fdaf385a2  
> Author: jzmaddock jzmaddock@gmail.com  
>   
> What am I doing wrong now?  
  
It hasn't been merged yet - now done.  
  
OK – got it.  
  
Thanks  
  
Paul

---

## Comment 44

> Username: pabristow  
> Created_at: 2014-09-01 14:24:26 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54065451  

1   When running the test with MSVC 2013, this   
#ifdef BOOST_MSVC  
#pragma warning (push)  
#pragma warning(disable:4127) // conditional expression is constant  
#endif  
  
in modular-boost\boost\math\distributions\hyperexponential.hpp wasn't effective - I had to add a warning disable to the build using  c++c, advanced, disable specific warning 4127:4389.   
  
There are lots of these  
modular-boost\libs\math\test\test_hyperexponential_dist.cpp(64): warning C4127: conditional expression is constant  
  
and this is in test_tools (Does this mean we are using an obselete version of test??? Looks OK)  
  
modular-boost\boost/test/tools/old/impl.hpp(106): warning C4389: '==' : signed/unsigned mismatch  
  
I think it would be nice to have a   
#ifdef _MSC_VER  
#pragma warning(disable:4127) // '==' : signed/unsigned mismatch  in test_tools  
#pragma warning(disable:4127) // conditional expression is constant  
#endif  
  
at the top of thes test_hyper_exponential.cpp.  
  
2 You have neatly demonstrated the utility of using code snippts to ensure that what it shown is what compiled and actually ran and actually output ;-)  
  
```  
   << boost::math::cdf(boost::math::complement(he), 15.0)  
```  
  
should be  
      << boost::math::cdf(boost::math::complement(he, 15.))  
:-)  
  
But I will added this example and use snippets to the Quickbook in due course if you wish.  
  
3  This is an excellent easy starter, but I wonder if we can find a slightly more interesting example.  
  
I've looked at  
  
The Long Tail: Myth or Reality?  
Pierre-Jean Benghozi and Françoise Benhamou  
International Journal of Arts Management  
Vol. 12, No. 3 (SPRING 2010), pp. 43-53  
Published by: HEC - Montréal - Chair of Arts Management  
Article Stable URL: http://www.jstor.org/stable/41065027  
but I didn't see how to get the rates data for rates and probs.  a network example would be even better, but again I haven't found any raw data to work with.  
  
4  Shall I try to make examples and mistakes with the various constructors?  Or have you done this already?  
  
Looking good.

---

## Comment 45

> Username: pabristow  
> Created_at: 2014-09-01 14:30:01 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54065940  

From: Marco Guazzone [mailto:notifications@github.com]   
Sent: 01 September 2014 11:57  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
Hi Paul,  
  
What you're looking is the hyperexponential branch (created by John).  
Instead, my commits are in the feature/hyperexponential_dist branch (since I don't have write permission) and needs to be merged in that branch.  
  
For the "different size" error, every constructor calls the function hyperexp_detail::check_dist which, among the various checks, tests for size consistency and raises a policy-based domain error with the following message:  
  
   "The parameters \"probabilities\" and \"rates\" must have the same length, but their size differ by: %1%."  
  
For the doc, take a look at the new Applications and Examples sections.  
  
OK - got your new stuff now.  
  
Thanks  
  
Paul

---

## Comment 46

> Username: sguazt  
> Created_at: 2014-09-01 15:01:08 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54068717  

Hi,  
  
1 Let me understand, should I replace BOOST_MSVC with _MSV_VER?  
   Also, the following two pragma lines seems identical:  
  
``` c++  
#ifdef _MSC_VER  
#pragma warning(disable:4127) // '==' : signed/unsigned mismatch in test_tools  
#pragma warning(disable:4127) // conditional expression is constant  
#endif  
```  
  
Did  you mean 4389 in place of 4127 in the first pragma?  
  
2 Sorry I fixed it in the _real_ code I forgot to do it in the doc. I will fix it  
  
3 I would avoid to delve into distribution fitting since currently the class does not provide any tools to do it (e.g., in literature there approach based on the Expectation-Maximization algorithm).  
    To show an example about another area of application, I can take the data from the following article  
  
```  
R. Wolski et al., "Using Parametric Models to Represent Private Cloud Workloads", IEEE Service Computing, PrePrint (http://doi.ieeecomputersociety.org/10.1109/TSC.2013.48)  
```  
  
   [http://128.111.41.26/research/tech_reports/reports/2013-05.pdf]  
  
where a 3-phase hyper-exp is used to fit the empirical distribution of Virtual Machine (VM) lifetime and interarrival time in a private cloud.  
In the example, I'd show the mean, the variance and maybe some CDF value  
OK?  
  
4 Tests for all constructors are in the `klass` test case.  
  
Cheers,  
Marco

---

## Comment 47

> Username: jzmaddock  
> Created_at: 2014-09-01 16:09:16 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54075510  

> 4  Shall I try to make examples and mistakes with the various constructors?  Or have you done this already?  
  
I have some constructor examples added to the version on my HD.  Only   
reason I haven't pushed is our HTML stylesheets are handling the   
headings spectacularly badly (I don't think h6 was ever expected!), but   
I'll add these shortly.  
  
John.

---

## Comment 48

> Username: pabristow  
> Created_at: 2014-09-01 16:13:20 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54075801  

From: Marco Guazzone [mailto:notifications@github.com]   
Sent: 01 September 2014 16:01  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
Hi,  
  
1 Let me understand, should I replace BOOST_MSVC with _MSV_VER?  
Also, the following two pragma lines seems identical:  
  
#ifdef _MSC_VER  
#pragma warning(disable:4127) // '==' : signed/unsigned mismatch in test_tools  
#pragma warning(disable:4127) // conditional expression is constant  
#endif  
  
Did you mean 4389 in place of 4127 in the first pragma?  
  
I did of course.  
  
(_MSC_VER is always defined for MS VS, whereas BOOST_MSVC isn’t defined until _after_ the first call to boost/config.hpp.  
  
So I think it is simplest to use it in places like this.  But perhaps John has other views?  )  
  
2 Sorry I fixed it in the real code I forgot to do it in the doc. I will fix it  
  
As a master of copy’n’paste errors, I’m in no position to throw stones ;-)  
  
But I expected to find hyper_exponential_example.cpp in math/example folder.  
  
I’ll leave John to add a file – unless you ask me to.  
  
and it should be compiled with the other examples, to make sure it does, even if the face of ‘improvements’ by maintainers ;-)  
  
3 I would avoid to delve into distribution fitting since currently the class does not provide any tools to do it (e.g., in literature there approach based on the Expectation-Maximization algorithm).  
To show an example about another area of application, I can take the data from the following article  
  
R. Wolski et al., "Using Parametric Models to Represent Private Cloud Workloads", IEEE Service Computing, PrePrint (http://doi.ieeecomputersociety.org/10.1109/TSC.2013.48)  
  
[http://128.111.41.26/research/tech_reports/reports/2013-05.pdf]  
  
where a 3-phase hyper-exp is used to fit the empirical distribution of Virtual Machine (VM) lifetime and interarrival time in a private cloud.  
In the example, I'd show the mean, the variance and maybe some CDF value  
OK?  
  
I have yet to digest it, but -  yes- sounds good.    
  
4 Tests for all constructors are in the klass test case.  
  
I was thinking of examples for the _end users_ (who will never look at the tests – or if they do, be confused/frighten-off by the clutter from the multiple FP types).  
  
The distributions are going to be used mostly by people who are statisticians or engineers rather than ‘proper computer scientists’ like you.  
  
I believe they need their hands-held/spoon-fed!  Examples are the best method.  
  
Paul  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/7#issuecomment-54068717 .  https://github.com/notifications/beacon/1076737__eyJzY29wZSI6Ik5ld3NpZXM6QmVhY29uIiwiZXhwaXJlcyI6MTcyNTIwMjg2OCwiZGF0YSI6eyJpZCI6NDA0MjM2Nzh9fQ==--ae3d2c100a9b00c95acfec9c1aeb4de06f8a951d.gif

---

## Comment 49

> Username: jzmaddock  
> Created_at: 2014-09-01 16:51:40 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54078437  

> I was thinking of examples for the _end users_ (who will never look at the tests – or if they do, be confused/frighten-off by the clutter from the multiple FP types).  
>   
> The distributions are going to be used mostly by people who are statisticians or engineers rather than ‘proper computer scientists’ like you.  
>   
> I believe they need their hands-held/spoon-fed!  Examples are the best method.  
  
I've just pushed a doc update that includes some construction examples.   
  Note that the heading sizes are all messed up: I've just posted on the   
ML about this as it looks like a quickbook bug.  
  
I'll look to adding an example code test file as well and moving the the   
code out of the docs to there so it does actually get tested.

---

## Comment 50

> Username: jzmaddock  
> Created_at: 2014-09-01 18:49:55 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54085041  

> But I expected to find hyper_exponential_example.cpp in math/example folder.  
>   
> I’ll leave John to add a file – unless you ask me to.  
>   
> and it should be compiled with the other examples, to make sure it does, even if the face of ‘improvements’ by maintainers ;-)  
  
Now done.  Everything looks OK by me, but can you cast your usual   
critical eye over the docs now as I think they're close to done?  
  
Cheers, John.

---

## Comment 51

> Username: sguazt  
> Created_at: 2014-09-01 19:35:54 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54087337  

Excellent, John.  
  
I've just removed from the doc, the ArgT type requirements.  
I've also added the pragma 4389 for MS VC (but not tested).  
  
I'm going to write an example for the Wolski paper.  
  
Marco

---

## Comment 52

> Username: sguazt  
> Created_at: 2014-09-01 21:53:11 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54093698  

Hi,  
I've updated the doc with the example from (Wolski et al.,2013).  
Hope it is ok.  
Interestingly, the results are slightly different from the one found in the paper. It is possible they used some tool like MATLAB/Octave for which there may be possible accuracy issues (see the initial discussions in this thread).  
Anyway, in the next days I will try to make some double check with Mathematica  
  
Cheers,  
Marco

---

## Comment 53

> Username: sguazt  
> Created_at: 2014-09-02 08:08:59 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54120810  

Hello,  
Another update... and I really hope the last one ;)  
  
I checked our results with Mathematica 10 and they agree, as you can see below:  
![wolski png-000001](https://cloud.githubusercontent.com/assets/671321/4115465/5666b66a-3278-11e4-9433-89f16db383a8.png)  
![wolski png-000002](https://cloud.githubusercontent.com/assets/671321/4115469/5a53b0b6-3278-11e4-8f5a-14485c24ce4b.png)  
  
So, I updated the doc to point out the  different results obtained by our implementation with the ones in (Wolski et al.,2013).  
Also, I expanded the accuracy section to discuss about possible issues with some implementation (e.g., MATLAB and Octave).  
  
Cheers,  
Marco

---

## Comment 54

> Username: sguazt  
> Created_at: 2014-09-02 08:27:52 UTC  
> Updated_at: 2014-09-02 09:05:41 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54122410  

OK. I retract my assertion :)  
Another update to fix an issue with Quickbook's itemized lists: a list was rendered "inline" and to make it appear OK I had to add an empty line before/after the list

---

## Comment 55

> Username: sguazt  
> Created_at: 2014-09-02 13:57:57 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54154865  

Yeah, it would be nice.  
Unfortunately,  
- I don't know the EMpht algorithm (i.e., the algorithm used by Wolski et al.)  
  http://home.imf.au.dk/asmus/pspapers.html  
- currently I have no time to study it and implement in a Boost-like fashion, and  
- more importantly I don't know if it represents the state-of-the-art for fitting hyperexp parameters.  
  
Indeed, likewise other probability distributions where the parameter estimation is not trivial, there are many several methods to estimate parameters (usually, based on moment matching and MLE). Here below is a non-exhaustive list to publications on this topic:  
- http://dx.doi.org/10.1080/15501320701259925  
- http://dx.doi.org/10.1109/TDSC.2006.27  
- http://www.sciencedirect.com/science/article/pii/S0166531605000738  
- http://dx.doi.org/10.1109/DSN.2002.1028966  
- http://doi.ieeecomputersociety.org/10.1109/QEST.2012.29  
  
So before implementing an estimation procedure one should study the state-of-the-art to understand where current research is moving to.  
Again, currently I have no time to do so as I have a couple of deadlines with my research projects (which actually are not on hyperexponential distributions, even though I use them).  
  
Cheers,  
  Marco

---

## Comment 56

> Username: pabristow  
> Created_at: 2014-09-02 14:23:01 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54158580  

OK – I agree this is a bridge too far - for now.  
  
EMpht certainly doesn’t look state-of-the-art – though it looks as though it still works.  
  
Maybe this is an exercise for a GSoC student?  
  
J  
  
Paul  
  
From: Marco Guazzone [mailto:notifications@github.com]   
Sent: 02 September 2014 14:58  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
Yeah, it would be nice.  
Unfortunately,  
-   I don't know the EMpht algorithm (i.e., the algorithm used by Wolski et al.) http://home.imf.au.dk/asmus/pspapers.html   
-   currently I have no time to study it and implement in a Boost-like fashion, and  
-   more importantly I don't know if it represents the state-of-the-art for fitting hyperexp parameters.  
  
Indeed, likewise other probability distributions where the parameter estimation is not trivial, there are many several methods to estimate parameters (usually, based on moment matching and MLE). Here below is a non-exhaustive list to publications on this topic:  
-   http://dx.doi.org/10.1080/15501320701259925  
-   http://dx.doi.org/10.1109/TDSC.2006.27  
-   http://www.sciencedirect.com/science/article/pii/S0166531605000738  
-   http://dx.doi.org/10.1109/DSN.2002.1028966  
-   http://doi.ieeecomputersociety.org/10.1109/QEST.2012.29  
  
So before implementing an estimation procedure one should study the state-of-the-art to understand where current research is moving to.  
Again, currently I have no time to do so as I have a couple of deadlines with my research projects (which actually are not on hyperexponential distributions, even though I use them).  
  
Cheers,  
Marco  
  
—  
Reply to this email directly or view it on GitHub https://github.com/boostorg/math/pull/7#issuecomment-54154865 .  https://github.com/notifications/beacon/1076737__eyJzY29wZSI6Ik5ld3NpZXM6QmVhY29uIiwiZXhwaXJlcyI6MTcyNTI4NTQ3OCwiZGF0YSI6eyJpZCI6NDA0MjM2Nzh9fQ==--6414e24b63baf000c3c112ecdd9ed4b7f072ee7a.gif

---

## Comment 57

> Username: sguazt  
> Created_at: 2014-09-02 14:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54160952  

Maybe it can be a GSoC.  
  
The main problem I see is that there is some _high-level_ math behind some of these techniques (especially the ones for Phase-Type distributions -- a family of probability distributions comprising the hyperexponential, the Erlang,... -- which use matrix analytic method involving matrix exponentials), that may take time to understand (and possibly to implement).  
  
Marco

---

## Comment 58

> Username: jzmaddock  
> Created_at: 2014-09-02 18:44:07 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54198338  

Marco, I've tracked down the problem with Octave/Matlab: these use a scale parameter and not a rate (confusingly the docs refer to the parameter as lambda which is usually used for the rate, although they do correctly state that this is the mean, where as nomally the mean is 1/lambda).  Anyway invert that parameter and you code should give correct results - I can't test locally as cygwin Octave doesn't handle array arguments to exppdf :-(  
  
Could the same issue effect the example code?  I skimmed the paper and I couldn't see a mention of which way they're parameterizing the exponential?  
  
HTH, John.  
  
PS also merged your recent changes.

---

## Comment 59

> Username: sguazt  
> Created_at: 2014-09-02 20:46:42 UTC  
> Updated_at: 2014-09-03 07:19:29 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54215060  

Hi John,  
For MATLAB/Octave, very good catch!  
I checked with MATLAB and results agree with out. I removed from the discussion about the accuracy.  
  
For the Wolski results, I've tried to use the lambdas in the paper as mean of exponentials (instead of rates), but the results were even worse. For instance, for VM interarrival time of DS1 we get a mean of `0.016716127400000` vs..`2202.1` (as claimed in the paper) and vs. `2181.24`(as obtained by our implementation and Mathematica).  
  
Also, I've tried to play with EMpht to try to fit the empirical interarrival times of DS1, but it seems that it get stuck after 5 iterations (I instructed EMpht to perform 50 iterations), and after 5 iterations i get the following hyperexponential:  
- probabilities=[0.588047, 0.282527, 0.129426]  
- rates=[0.219664, 0.000360, 0.007850]  
  
which is different from the one in the paper, Table VIII (cell (1,1)).  
Note, a possible source of difference for the fit is due to the fact that the EM algorithm is randomly initialized.  
  
So, returning on the different result, currently I lack of a reasonable explanation :-/  
  
Marco

---

## Comment 60

> Username: sguazt  
> Created_at: 2014-09-04 14:11:45 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54482536  

Hi guys,  
  
Firstly, John I've seen you improved the `normalize` function to make sure probs sum to 1.  
Thanks!  
  
Also, I've added some more tests to check the special cases when the hyperexp degenerates into an exp distribution (as discussed in the _Related distributions_ section of the doc).  
  
Said this, are we done with the implementation? :)  
  
Cheers,  
Marco

---

## Comment 61

> Username: pabristow  
> Created_at: 2014-09-04 15:43:01 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54497169  

From: Marco Guazzone [mailto:notifications@github.com]   
Sent: 04 September 2014 15:12  
To: boostorg/math  
Cc: Paul A. Bristow  
Subject: Re: [math] New Feature: hyperexponential distribution (#7)  
  
Hi guys,  
  
Firstly, John I've seen you improved the normalize function to make sure probs sum to 1.  
Thanks!  
  
Also, I've added some more tests to check the special cases when the hyperexp degenerates into an exp distribution (as discussed in the Related distributions section of the doc).  
  
Said this, are we done with the implementation? :)  
  
Well I haven’t noted any other glitchettes – but as Helmuth von Moltke the Elder  http://en.wikipedia.org/wiki/Helmuth_von_Moltke_the_Elder http://en.wikipedia.org/wiki/Helmuth_von_Moltke_the_Elder  
  
would say today  
  
"no plan survives contact with the users,"  
  
I’m away for two weeks J  
  
Paul

---

## Comment 62

> Username: pabristow  
> Created_at: 2014-09-05 13:04:45 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54621894  

A couple more thoughts.  John and I both fell into a pit trying to do the obvious  
  
```  
std::copy(he.probabilities().begin(), he.probabilities().end(), std::ostream_iterator<double>(std::cout, " "));  
```  
  
Naive users (mea culpa) will expect this to work.  
  
John suggests that the safest option is to change to return a const reference.  
  
The docs should make clear that the user will get _normalized_ probabilities.  
  
The Wolksi example looks helpful (though I think it would _look_ much better with fewer insignificant significant digits ;-)  
I doubt if more than 2 are meaningful.  It is useful to confirm that the computed values agree, but the final output is ugly).  
  
I am still unconvinced that the graphs show the effect of different rates and probs well, but I'll work on a better one when I get back  
  
We are probably ready to merge to develop and expose the tests to the extensive bank of testers using every compiler and OS know to man.  Past experience is that this always throws up some wrinkles.

---

## Comment 63

> Username: sguazt  
> Created_at: 2014-09-05 13:32:51 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54624923  

On Fri, Sep 5, 2014 at 3:04 PM, Paul A. Bristow notifications@github.com  
wrote:  
  
> A couple more thoughts. John and I both fell into a pit trying to do the  
> obvious  
>   
> std::copy(he.probabilities().begin(), he.probabilities().end(), std::ostream_iterator<double>(std::cout, " "));  
>   
> Naive users (mea culpa) will expect this to work.  
>   
> John suggests that the safest option is to change to return a const  
> reference.  
>   
> OK, I'm not really convinced about this change but majorivity wins :)  
  
The docs should make clear that the user will get _normalized_  
  
> probabilities.  
>   
> OK I will add this note  
>   
> The Wolksi example looks helpful (though I think it would _look_ much  
> better with fewer insignificant significant digits ;-)  
> I doubt if more than 2 are meaningful. It is useful to confirm that the  
> computed values agree, but the final output is ugly).  
>   
> OK I will fix the note to round to 2 decimal digits  
>   
> I am still unconvinced that the graphs show the effect of different rates  
> and probs well, but I'll work on a better one when I get back  
>   
> OK. One possibility is to use the same example used by Mathematica, where  
> rates are kept fixed and only probabilities change.  
> Also, another possibility is to create two different graphs:  
> - One where rates are kept fixed and probabilities vary  
> - Another one where probabilities are kept fixed and rates change  
>   And possibly one could add a third additional graph to show a feq hyperexps  
>   with different phases but with the same mean value (starting from a number  
>   of phases equal to 1, that is from the exponential distribution)  
>   
> We are probably ready to merge to develop and expose the tests to the  
> extensive bank of testers using every compiler and OS know to man. Past  
> experience is that this always throws up some wrinkles.  
>   
> Very good  
> I'll send you a comment once I'm done with these new changes.  
  
Cheers,  
  
-- Marco

---

## Comment 64

> Username: sguazt  
> Created_at: 2014-09-07 17:04:38 UTC  
> Updated_at: 2014-09-07 17:05:53 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54752980  

Hello,  
All changes done. Also, I've added some more graphs. Rebuild the doc and take a look... And let me know your opinion :)  
  
I would like just to spend two words to motivate why the change of the return type from `std::vector<T>` to `std::vector<T> const&` does not convince me:  
1. We are somewhat exposing the internal representation to the users of the class.  
2. If, for some reason, in the future we want to change the internal data structures (e.g., to use `std::valarray` in place of `std::vector`) we need to add extra code to return a valid `const&`.  
3. The standard ISO C++ committe seems to not bother of this type of user requirement (e.g., see the return type of `std::discrete_distribution::probabilities` method, in Sec. 26.5.8.6.1 of http://open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3797.pdf )  
  
Anyway, you can find this change in the commit `a8efc9a` as well.  
  
Cheers,  
Marco

---

## Comment 65

> Username: jzmaddock  
> Created_at: 2014-09-08 17:09:04 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54853053  

Thanks for the changes: on reflection you have a point about returning probabilities() by value - the calculation is inherently parallelizable (via the upcoming Boost.SMID for example) so we _may_ indeed wish to change things internally - though it would require use cases with very large numbers of phases to be worth while.  I wonder too if accessors like probabilities() actually see little use out side of debugging code and test cases?  
  
OK, here's what I'll do: I'll revert the change to probabilities() etc and then merge everything to develop so we start getting tested.  Paul can complain when he gets back ;-)

---

## Comment 66

> Username: sguazt  
> Created_at: 2014-09-08 17:17:38 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54854184  

Good!  
  
Thank you so much.  
  
Marco

---

## Comment 67

> Username: jzmaddock  
> Created_at: 2014-09-08 17:19:29 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54854454  

All merged, and hyper_exponential branch deleted.

---

## Comment 68

> Username: sguazt  
> Created_at: 2014-09-09 16:30:15 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54997514  

Hi John,  
  
I fixed a typo in the description of the two SFINAE-based constructors.  
  
Could you merge it in the `develop` branch?  
  
Thanks,  
Marco

---

## Comment 69

> Username: jzmaddock  
> Created_at: 2014-09-09 16:46:20 UTC  
> Url: https://github.com/boostorg/math/pull/7#issuecomment-54999783  

Good catches, merged.

---
