# #132 - [Feature request] Request the ability to `sync` single component of chain [Open]

> Username: Nick-The-Uncharted  
> Created at: 2021-04-27 01:43:19 UTC  
> Updated at: 2021-04-27 01:43:19 UTC  
> Url: https://github.com/boostorg/iostreams/issues/132  

As I describe in [here](https://stackoverflow.com/questions/67268620/how-to-only-flushsync-one-component-of-boost-filtering-stream). I need flush only the first component of chain, but `sync_impl` method of indirect_streambuf is private. I can't think of a better way to achieve this.
