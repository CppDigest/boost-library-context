# #117 - Explain bintype concept and optional parts [Closed]

> Username: HDembinski  
> Created at: 2018-10-05 09:40:20 UTC  
> Updated at: 2019-03-05 00:52:47 UTC  
> Closed at: 2019-03-05 00:52:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/117  

Alex wrote:  
> you can support my type of user by better formalizing the concepts you have been using implicitly and be more clear in your documentation about what is required and what is optional.  
  
> And, what functionality is gained by implementing the optional parts. For this example, you could say there are no requirements on the bin_type, because it is not used internally in the histogram library. However, if you conform to NumberedBin then the use pattern h.at(bina, binb, binc) becomes available. All built-in bin_types are NumberedBins.
