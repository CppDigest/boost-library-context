# #101 - Jim's review [Closed]

> Username: HDembinski  
> Created at: 2018-09-24 10:56:35 UTC  
> Updated at: 2018-12-18 11:12:49 UTC  
> Closed at: 2018-12-18 11:12:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/101  

Hi, I'd like to give an outsider review of Hans Dembinski's Histogram library. I'm a developer of software for particle physicists with a background of using histograms (heavily) in analysis. Over the past 12 years, I've been developing histogramming libraries in Python (plothon, SVGFig, Cassius, Augusts, Histogrammar, and histbook), working on the problem of making histogram-based analysis easier for physicists.  
  
Here are some questions you might want to answer in your review:   
- What is your evaluation of the design?   
It is a clean, simple design that is extensible in principle to the cases we want to cover. It is missing "profile plots" (not really histograms because they represent functions, not distributions, but binned like histograms and good to use in the same analysis), but these could be implemented as a new storage type. Similarly, some concurrent applications would need atomic-valued bins, but these, too, can be storage types.  
  
I like the fact that this library doesn't attempt to define a file format or statistics and plotting interfaces, since these would cause feature-creep: I/O formats and graphics are open-ended problems, but histogramming is a well-defined nugget. Feature requests, like profile plots and atomic bins, are not only implementable in storage types, but come from a short list of possibilities. Profile plots were invented 40 years ago, and a third type of histogram-like object hasn't been needed in particle physics analysis yet.  
  
As a C++ project, being header-only is a big advantage. Whereas I've found it difficult to install Boost libraries in the past (usually as dependencies for a project I wanted to install), header-only libraries are very easy. I'm glad that many Boost projects are going in that direction.  
  
- What is your evaluation of the implementation?   
Clean, modern C++, easy to read. Again, the axis type/storage type design is well thought-out. In fact, that idea appeared independently in ROOT's version 7 (future) histogram interface, and in my Histogrammar and histbook.  
  
- What is your evaluation of the documentation?   
Also very readable. I was able to get up to speed on the project in only a half hour of reading.  
  
- What is your evaluation of the potential usefulness of the library?   
Specifically, I'm looking forward to rewriting histbook to use Boost Histogram instead of Numpy. That increases the installation burden of my Python library, but I expect this header-only library can be wrapped up as a pip-installable Python extension module with pybind11. The existence of a histograms-only library standardized in a well-known ecosystem like Boost would allow me to split my project into simple, exchangeable plain histograms (link to Boost) and large-scale histogram organization for multi-histogram fits (histbook). The latter is needed for organizing analyses that fit thousands of histograms simultaneously, as is common in modern particle physics analyses, but the former— plain histograms— can be a separate problem. Having that plain histogram layer be exchangeable between C++ and Python is another benefit.  
  
- Did you try to use the library? With what compiler?   
Yes, gcc 7.3.0.  
  
  Did you have any problems?   
Only with Boost; I had to learn the b2 bootstrapping procedure. Once that worked, it was fine. I only needed that because a previous version of Boost Histogram used Boost Python, which is not header-only. I'm looking forward to a version of this wrapped in pybind11.  
  
- How much effort did you put into your evaluation?   
  A glance? A quick reading? In-depth study?   
I started with the example code and tested a few variations in C++ and Python (old code based on Boost Python).  
  
- Are you knowledgeable about the problem domain?   
  
Yes.   
And finally, every review should answer this question:   
- Do you think the library should be accepted as a Boost library?   
  
Yes, I think it should. The improvements I'm looking forward to can be implemented in the short term using the extension capabilities that it already has, though I think profile plots are fundamental enough that they should be part of a future version of the library (and can be a non-breaking addition to the library).  
  
Thanks!  
-- Jim

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-12-18 11:12:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/101#issuecomment-448185508  

Closing this, as profile usage and parallel filling has been added to the library. What's missing is proper documentation, for which I created new issues.
