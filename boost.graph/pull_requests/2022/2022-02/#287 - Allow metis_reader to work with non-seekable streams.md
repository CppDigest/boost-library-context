# #287 Allow metis_reader to work with non-seekable streams [Closed]

> Username: J5lx  
> Created at: 2022-02-18 23:59:05 UTC  
> Updated at: 2022-02-19 22:35:22 UTC  
> Closed at: 2022-02-19 22:35:22 UTC  
> Url: https://github.com/boostorg/graph/pull/287  

metis_reader currently unconditionally seeks to the beginning of the stream when it starts, which causes it to fail with a metis_input_exception when reading from streams that are not seekable. For example, in my case I’m trying to read a graph that is piped into my program via stdin, which does not work because of this. Clearing the error state flags of the stream after seeking allows metis_reader to work with non-seekable streams without potentially breaking code that relies on metis_reader seeking to the beginning of (seekable) streams.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-02-19 06:39:19 UTC  
> Url: https://github.com/boostorg/graph/pull/287#issuecomment-1045888184  

Nice idea, but surely this will clear away legitimate errors when they happen too?  
I'm a bit naive on file IO, but surely there must be a general solution to this already.   
The Metis code doesn't need to know about files, it just needs an iterator over strings. Something else can be responsible for opening files or pipes or whatever and creating the appropriate iterator over them.  
  
So, in short, I'm not inclined to merge this one, sorry. Does file open not seek to the beginning automatically anyway? I can imagine there being all sorts of platform-dependent answers to that.

---

## Comment 2

> Username: J5lx  
> Created_at: 2022-02-19 22:35:22 UTC  
> Url: https://github.com/boostorg/graph/pull/287#issuecomment-1046116957  

Hmm, I wrote this under the impression that clearing legitimate errors would not be an issue, but looking at it again now, I’m no longer quite say how I arrived at that conclusion. Perhaps the fact that there is no proper way to check whether seek operations are supported by a stream other than “try and see if it fails” has led me in that direction. Thanks for catching that.   
  
Nevertheless, that requirement for the stream to be seekable still seems kind of arbitrary and unwarranted to me. I’d expect readying the stream for reading the graph (which must be the purpose of that line given that it’s the very first thing the reader does) to be the responsibility of the code calling the metis_reader. I would prefer for the reader to “just work” with streams where seeking is not possible. However, I see that the current behaviour has been in there since forever, and if there’s nothing to be done about it, I’ll just resort to a workaround and instead submit a PR to graph_parallel to at the very least document this requirement.

---
