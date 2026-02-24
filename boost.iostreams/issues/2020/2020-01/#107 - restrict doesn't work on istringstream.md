# #107 - restrict doesn't work on istringstream [Closed]

> Username: drtconway  
> Created at: 2020-01-05 03:39:30 UTC  
> Updated at: 2025-07-15 10:21:51 UTC  
> Closed at: 2025-07-15 10:21:51 UTC  
> Url: https://github.com/boostorg/iostreams/issues/107  

I'm wanting to use boost::iostreams::restrict to present parts of a file given as an istream as if they were first class istreams.   
  
My first test case which works on an ifstream works fine. The second test case which is identical except it uses an istringstream instead fails - calling read() reads from the start of the content rather than from the offset position.  
  
I realise this may be a flaw in the stringstream implementation, rather than a bug in iostreams. Can anyone shed light on this, and if so, suggest an alternative to using stringstreams?
