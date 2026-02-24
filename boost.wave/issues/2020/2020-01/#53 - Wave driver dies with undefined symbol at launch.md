# #53 - Wave driver dies with undefined symbol at launch [Closed]

> Username: jefftrull  
> Created at: 2020-01-10 05:51:41 UTC  
> Updated at: 2020-03-06 22:47:44 UTC  
> Closed at: 2020-03-06 22:47:44 UTC  
> Url: https://github.com/boostorg/wave/issues/53  

Since sometime before 1.61 the "wave" driver, which is not built by default, dies when you launch it, complaining about not finding `defined_grammar_gen::parse_operator_defined(...)`. When I look carefully at what `libboost_wave.so` supplies vs. what wave expects I find the difference is the use of `std::mutex` in the library vs. `null_mutex` in the wave driver. Adding the following to `build/Jamfile.v2` in the project section seems to work around the problem:  
  
```  
      <define>BOOST_WAVE_SUPPORT_THREADING=0  
```  
which matches the definition in `tool/build/Jamfile.v2`. I couldn't say whether that was the "right" fix.

---

## Comment 1

> Username: hkaiser  
> Created at: 2020-01-10 13:07:35 UTC  
> Url: https://github.com/boostorg/wave/issues/53#issuecomment-573028377  

@jefftrull that is definitely the right fix as the driver does not use more than one thread, so no multi-threading issues could arise, thus it uses the `null_mutex` for the spirit parsers. Thanks for figuring that out!

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-01-28 23:33:47 UTC  
> Url: https://github.com/boostorg/wave/issues/53#issuecomment-579523881  

@hkaiser just to be sure, using my workaround would mean that the Wave libraries themselves would be built with `null_mutex` and any users depending on them containing `std::mutex` would need to do their own builds... that OK?

---

## Comment 3

> Username: hkaiser  
> Created at: 2020-01-29 00:07:52 UTC  
> Url: https://github.com/boostorg/wave/issues/53#issuecomment-579532909  

@jefftrull alternatively you could rebuild the library just for the driver with threading disabled.   
  
OTOH, if we documented that the pre-built wave library is not thread-safe (which is what most people might prefer anyway as there is not really a point in using the same instance of the preprocessor concurrently from several threads), the solution you suggested is definitely viable.

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-02-16 19:54:09 UTC  
> Url: https://github.com/boostorg/wave/issues/53#issuecomment-586745640  

I am now 70% sure this is a user error on my part. `libboost_wave.so` actually gets built twice, one for distribution (where `std::mutex` is used) and once for the purposes of the wave tool itself (null_mutex), with `RUNPATH` set appropriately. If you set `LD_LIBRARY_PATH` to the `stage/lib` directory you pick up the wrong one for the wave tool.

---

## Comment 5

> Username: jefftrull  
> Created at: 2020-03-06 22:47:44 UTC  
> Url: https://github.com/boostorg/wave/issues/53#issuecomment-595997334  

Concluding this is user error :)
