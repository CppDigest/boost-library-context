# #16 - add a templated histogram with a storage parameter [Closed]

> Username: HDembinski  
> Created at: 2016-06-17 13:40:57 UTC  
> Updated at: 2016-09-13 07:27:25 UTC  
> Closed at: 2016-09-13 07:27:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/16  

The standard type (that uses the nstore) should still be called histogram. The template that allows to parametrize the storage type, could be called static_histogram. The shared interface should be put into static_histogram, histogram would derive from it and specialize what's needed for the nstore. The method "depth()" is only relevant for histogram.
