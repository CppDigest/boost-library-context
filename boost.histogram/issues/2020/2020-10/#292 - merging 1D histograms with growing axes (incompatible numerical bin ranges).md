# #292 - merging 1D histograms with growing axes (incompatible numerical bin ranges) [Closed]

> Username: jvo203  
> Created at: 2020-10-23 02:00:02 UTC  
> Updated at: 2020-10-28 11:12:39 UTC  
> Closed at: 2020-10-28 11:12:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/292  

Just like ROOT, Boost::Histogram has an option to grow axes automatically and it works great with a single histogram (right now multiple threads use a mutex to lock one shared histogram for the updates). It works reasonably well in a multithreaded environment but the mutex introduces a bottleneck.  
  
I would like to remove this bottleneck by constructing multiple thread-local 1D histograms with automatically growing axes. The bins are not categorical, just plain floating-point numerical data. Then after all the OpenMP threads have done their work the thread-local histograms would be combined into a single final one.  
  
Is there an easy way to merge automatically-growing numerical histograms with different bins (due to each thread having slightly different bins ranges/number of bins)? Other than that your library works great, and I would like to avoid having to use the mammoth ROOT dependency in this particular project.  
  
All I can think of is to create a merged histogram in the final step with the axis range based on the data min/max levels, and then adding thread-local bin centres weighted by thread-local bin counts from the thread-local histograms. In the final step the all-data min/max values are already known so there is no need to grow the axis. During the initial multi-threaded data pass there is no way to know the all-data min/max levels in advance, hence the need to grow the axes automatically.

---

## Comment 1

> Username: jvo203  
> Created at: 2020-10-23 08:33:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/292#issuecomment-715181524  

For your reference, this parallel histogram merge currently works well but is there a better "official" way of merging histograms with incompatible axes?  
  
```  
// fits.hpp  
using namespace boost::histogram;  
  
using histogram_t = decltype(make_histogram(axis::regular<Ipp32f,  
                                                          use_default,  
                                                          use_default,  
                                                          axis::option::growth_t>()));  
  
std::vector<std::optional<histogram_t>> hist_pool;  
  
// fits.cpp  
  
  
  // this version is not thread-safe  
  //auto _data_hist = make_histogram(axis::regular<>(NBINS, dmin, dmax));  
  
  // this should be thread-safe  
  auto _data_hist = make_histogram_with(dense_storage<accumulators::thread_safe<long>>(),  
                                        axis::regular<>(NBINS, dmin, dmax));  
  
// merge the thread-local histograms in parallel  
#pragma omp parallel for shared(_data_hist)  
  for (auto &entry : hist_pool)  
  {  
    if (entry.has_value())  
    {  
      auto &_hist = entry.value();  
  
      for (auto &&x : boost::histogram::indexed(_hist))  
      {  
        auto bin = x.bin();  
        auto centre = 0.5 * (bin.lower() + bin.upper());  
        auto count = *x;  
        _data_hist(centre, weight(count));  
      }  
    }  
  }  
  
  std::cout << "MERGED ALL-DATA HISTOGRAM" << std::endl;  
  
  std::ostringstream os;  
  os << _data_hist;  
  std::cout << os.str() << std::endl;  
```

---

## Comment 2

> Username: HDembinski  
> Created at: 2020-10-28 11:12:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/292#issuecomment-717864242  

> Other than that your library works great, and I would like to avoid having to use the mammoth ROOT dependency in this particular project.  
  
Thank you!  
  
There is no obvious solution to merging histograms with continuous axes that do not have aligned bin edges. There are several ways to go about that and therefore we cannot offer a library solution for this, there are too many choices. You found a solution that works for you and that is good. I see no better/builtin way to do this.
