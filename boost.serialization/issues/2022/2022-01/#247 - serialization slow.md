# #247 - serialization slow? [Closed]

> Username: nickhuangxinyu  
> Created at: 2022-01-03 09:31:10 UTC  
> Updated at: 2022-01-07 14:35:16 UTC  
> Closed at: 2022-01-07 14:35:16 UTC  
> Url: https://github.com/boostorg/serialization/issues/247  

I have a class:  
  
```  
class A {  
 public:  
  A() { read();}  
  void read() {  
     // read a lot of files, save data into container  
  }  
  std::unordered_map<std::string, std::map<std::string, double>> data_;  // read from files  
};  
  
```  
it read a lot of files, so it will be slow(8s to read), but the files changed once a week.  
  
So, i want to use `serialization `to speed up.  
  
but after i use `boost.serialization` `binary `format, the output file is 700MB.  
  
the `deserialization `cost 30s, slower than read.  
  
is there anything i need to pay attention?

---

## Comment 1

> Username: correaa  
> Created at: 2022-01-03 10:00:27 UTC  
> Updated at: 2022-01-03 10:07:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/247#issuecomment-1003981200  

Your data structure is very complicated and requires a lot of dynamic memory and indirection for transversal.  
Given that, I think your timings are reasonable for the data structure that you are using.  
  
Binary format perhaps is compact but (like any other archive format) it cannot take advantage of any optimization for such a complicated data structure.  
  
In general, I would recommend rethinking why you need such a complicated structure for `data_` and think of replacing it by `std::vector<std::tuple<std::string, std::string, double>>` or `boost::flatmap<std::pair<std::string, std::string>, double>`.  
Or something even simpler (see below).  
  
It could be that you will see gains overall in your application if you just use a simpler data structure `std::[unordered_]map<std::pair<string, string>, double>`.  
  
Also, if you know of a maximum length for the strings then you can replace `std::string` by `std::array<char, MAX_LABEL_SIZE>`.  
  
Going back to serialization, if you can afford the memory you can try "flattening" your data into 3 vectors `std::vector<std::array<char, N>>`, `std::vector<std::array<char, N>>`, `std::vector<double>` and (de)serialize that.  
  
(De)serializing 700MB of flat memory to binary shouldn't take more than 1second in new SSD HDs.  
(I did my own tests recently https://gitlab.com/correaa/boost-multi#serialization.)

---

## Comment 2

> Username: robertramey  
> Created at: 2022-01-03 22:46:50 UTC  
> Url: https://github.com/boostorg/serialization/issues/247#issuecomment-1004408280  

First ask yourself/verify how time it takes to write and read a 700MB file on your system which is stored in the kubernetes system.  That is, how long the system safe/load 700MB without using serialization. Experiment a little with i/o buffer sizes to get the best time.  This time would seem to the best we can hope for.   Compare this to the boost serialization timings (with the same buffer_sizes) .  If they are the same, we're done.  Otherwise ...  
  
Software using libraries incorporate a lot of "hidden" code.  When concerns arise regarding performance of  such software, the first thing is to figure out which library/component is consuming the most time.  This is impossible to discern by inspection - since a large part of the implementation is hidden.  In your case you've got a few opaque components any of which might be the culprit.:  
  
1.  Code inside the serialization library  
2. Implementation code in the standard library - in particular the std i/o system.  In particular read and write.  
3. latencies of devices on which standard i/o calls are implemented.  
3. implementation of std::unordered_map  
4. latency of kubernetes.  
  
So the #1 think you need to do is run your bench make under the execution time profiler on your system.   Usually it takes a little effort to set this up,  But here is no way to escape this.  This will provide you with a list of all the functions in your system and the number of times each is called and the amount of cpu time and clock time spent in each one.  If code in the serialization library is near the top of the list and thus slowing things down, get back to me.  Now might want to make some small changes which help performance.    
  
1. If the time is consumed in the file i/o system (read, write, etc.) -  consider using larger i/o buffer sizes.  
2. If the time is consumed in the kubernetes system - consider using a fancy input/output buffering package.  Note I don't know anything about this - just a wild guess.  
3. if there are any obvious slow spots in the serialization library code (e.g. unordered_map performance varies a lot between implementations!),  Take a close look at those.  
5. consider using text archive rather than binary archive.  It is smaller, but consumes more cpu time.  
6. consider compressing the data on storage and decompressing on loading.  Fact is cpu time consumed in serialization is much less than time spent on i/o.  So using data compression make the archive smaller and faster.  Best of all, it's trivial to use.  Boost contains a library called "streams".  This lets you add a compressor and decompressor to an iostream without doing any coding yourself.  Not hard to set up.  It adds cpu time of course - but shrinks the file so it cuts i/o latency by about half.  If you're interested you can "stack" stream functions so you could add encryption/decryption to the serialization step without programming overhead.  
  
Soooooo - I'm guessing you can cut your save/load times by half. But I'm also guessing you'll have to do the above.  And I'm going to guess it will take a couple of days to accomplish. Ask you're boss if he things it worth it.
