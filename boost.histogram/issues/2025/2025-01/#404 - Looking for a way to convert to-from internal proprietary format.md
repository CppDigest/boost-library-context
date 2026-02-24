# #404 - Looking for a way to convert to/from internal proprietary format. [Closed]

> Username: tfiner  
> Created at: 2025-01-23 22:05:54 UTC  
> Updated at: 2025-01-24 00:34:50 UTC  
> Closed at: 2025-01-24 00:34:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/404  

I have some existing serialized histogram files for an internal library (not boost::histogram).  I'd like to use the boost histogram in newer code, and be able to load and save these old files.  Our old histograms are one dimensional fixed bin widths, and are equivalent to a boost::histogram with a single regular axis.  
  
I think I have everything I need to export a boost::histogram into the old format.    
  
Hoeever, I can't see a way to restore the bin counts.  The only was I can think of to fill in a templated JSON/XML, feed that to boost::serialization, then boost::histogram.  I really don't like this solution because it is brittle and hacky.  
  
What I'd really like to do is to be able to read and write bins directly, or be able to reconstruct the histogram via a factory/builder where I pass in the bins to the histogram.  
  
Is there a way to do this?

---

## Comment 1

> Username: tfiner  
> Created at: 2025-01-24 00:34:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/404#issuecomment-2611297336  

Ah! I didn't realize that you could assign to as well as read from the iterator:  
`  
for (auto&& bin : bh::indexed(h, bh::coverage::all)) {  
   // read count from somewhere...  
   *bin = count;  
}  
`
