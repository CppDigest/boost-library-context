# #52 - Why is it ok to add multiple option_description's with the same long name? [Closed]

> Username: eyalroz  
> Created at: 2018-04-08 09:06:26 UTC  
> Updated at: 2018-04-08 09:14:08 UTC  
> Closed at: 2018-04-08 09:14:08 UTC  
> Url: https://github.com/boostorg/program_options/issues/52  

It seems that `options_descriptions.add()` doesn't check for repeat addition of an option with the same name. This is even illustrated by [`void test_ambiguous_short()` within `exception_test.cpp`](https://github.com/boostorg/program_options/blob/develop/test/exception_test.cpp#L22) -- where the exception the test catches only regards the ambiguity of `-c`, not of `--output`.  
  
What am I missing here?

---

## Comment 1

> Username: eyalroz  
> Created at: 2018-04-08 09:14:08 UTC  
> Url: https://github.com/boostorg/program_options/issues/52#issuecomment-379534453  

Oh, it seems it is caught, but only if you actually use the ambiguous name. never mind.
