# #159 - Object Lifetimes documentation missing. [Open]

> Username: roccomoretti  
> Created at: 2023-02-07 21:06:40 UTC  
> Updated at: 2023-02-07 21:06:40 UTC  
> Url: https://github.com/boostorg/iostreams/issues/159  

The User Guide section "3.7 Object Lifetimes" (https://github.com/boostorg/iostreams/blob/develop/doc/guide/lifetimes.html) has been labeled as `[To be supplied in the next release]` for approximately the past [18 years](https://github.com/boostorg/iostreams/commit/97de7d8c76921f23e42d9f7213a2e69825ac4484#diff-c85900f047f3caa5390fe91667866940a8bd08eb414ca85f8c45ba94ab549297).  
  
Some documentation on the lifetime assumptions about the sources/sinks/filters (e.g. do streams/steam_buffers do any lifetime management of the objects passed to them, or is it all up to the caller?) In particular, what happens when the devices are filesystem objects? (e.g. do the streams do any open/close/flush management?) [This mailing list post](https://groups.google.com/g/boost-list/c/w6RcFNuOG4A) indicates that it might be more complicated than one might first think -- though that post is ~18 years old, so things might be simplified with the next release :wink:.
