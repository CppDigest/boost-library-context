# #122 - discuss parallelization options in user guide [Closed]

> Username: HDembinski  
> Created at: 2018-10-22 12:23:36 UTC  
> Updated at: 2019-02-24 21:05:16 UTC  
> Closed at: 2019-02-24 21:05:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/122  

- fill thread-local copies of histograms in worker threads and add them in main thread  
- fill shared histogram in multiple worker threads (using atomic type counter)
