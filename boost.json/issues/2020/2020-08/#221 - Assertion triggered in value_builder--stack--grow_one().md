# #221 - Assertion triggered in value_builder::stack::grow_one() [Closed]

> Username: pauldreik  
> Created at: 2020-08-28 05:14:50 UTC  
> Updated at: 2020-09-01 15:03:43 UTC  
> Closed at: 2020-09-01 15:03:43 UTC  
> Url: https://github.com/boostorg/json/issues/221  

I ran fuzzer_parse on the develop branch, and it crashes with the following (snipped) stack trace  
```  
Running: /tmp/minimized-from-76b983e9e7a1f8398c2db28d55ff1a84eec2e8f6  
fuzzer_parse: ../include/boost/json/impl/value_builder.ipp:129: void boost::json::value_builder::stack::grow_one(): Assertion `(capacity & (capacity - 1)) == 0' failed.  
  
  #9 0x56a5d6 in boost::json::value_builder::stack::grow_one() /home/banan/code/json/fuzzing/../include/boost/json/impl/value_builder.ipp:128:5  
    #10 0x569c13 in boost::json::value& boost::json::value_builder::stack::push<long&, boost::json::storage_ptr&>(long&, boost::json::storage_ptr&) /home/banan/code/json/fuzzing/../include/boost/json/impl/value_builder.ipp:251:9  
    #11 0x5b3467 in boost::json::value_builder::insert_int64(long) /home/banan/code/json/fuzzing/../include/boost/json/impl/value_builder.ipp:516:9  
    #12 0x5b3467 in boost::json::parser::handler::on_int64(long, std::basic_string_view<char, std::char_traits<char> >, std::error_code&) /home/banan/code/json/fuzzing/../include/boost/  
```  
  
Here is the offending test case minimized-from-76b983e9e7a1f8398c2db28d55ff1a84eec2e8f6:  
```  
[1,9,9,9,9,9,9,9,9,9,9,1,9,9,9,9,9,9,9,1,9,1,9,9,9,9,9,9,1,9,9,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,1,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,9,9,9,9,9,1,1,9,9,9,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,1,9,1,9,9,9,9,9,9,9,9,9,9,1,9,9,9,9,9,1,9,9,9,9,9,9,9,1,9,1,9,9,9,9,9,9,9  
```  
I ran git bisect, and it was introduced in de024555c48909c500fd4321aa5a1a3ba19f5fea which currently is on develop but not on master.  
  
So why was this not detected by the CI fuzzer?   
- the corpus is old (bintray credentials), please finish #18   
- fuzzing runs for too short time (bad corpus -> needs longer time)  
- crontab fuzzing is made on master, not develop (?)

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-28 07:14:46 UTC  
> Url: https://github.com/boostorg/json/issues/221#issuecomment-682369074  

@pauldreik Earlier today I refactored `value_builder` and the assert did go off, but it shouldn't be there anymore after some of our recent changes. Once my PR is merged if will be fixed. Thanks as always :)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-01 14:58:00 UTC  
> Url: https://github.com/boostorg/json/issues/221#issuecomment-684917064  

Is this fixed?

---

## Comment 3

> Username: pauldreik  
> Created at: 2020-09-01 15:02:08 UTC  
> Url: https://github.com/boostorg/json/issues/221#issuecomment-684920135  

I belive so, but the test case should be added to the old_crashes  
folder, so it will never happen again. The CI fuzz job reruns the old  
crashes.  
  
Den 2020-09-01 kl. 16:58, skrev Vinnie Falco:  
> Is this fixed?  
>   
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/CPPAlliance/json/issues/221#issuecomment-684917064>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AA3AT6WXVXIT5BTUWLVXY5TSDUDYRANCNFSM4QNXL4NQ>.  
>

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-01 15:03:43 UTC  
> Url: https://github.com/boostorg/json/issues/221#issuecomment-684921291  

I don't mind merging it if you submit a pull request. Closing this for now.
