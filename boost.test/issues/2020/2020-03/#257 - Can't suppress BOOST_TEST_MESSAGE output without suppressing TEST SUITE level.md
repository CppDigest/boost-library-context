# #257 - Can't suppress BOOST_TEST_MESSAGE output without suppressing TEST SUITE level [Open]

> Username: kostja  
> Created at: 2020-03-03 14:19:40 UTC  
> Updated at: 2020-05-07 23:02:29 UTC  
> Url: https://github.com/boostorg/test/issues/257  

My tests use a lot of BOOST_TEST_MESSAGE calls to generate test trace. Unfortunately, this completely bloats XML output I use to integrate boost and Jenkins, since every message is appended to the xml file. If I reduce the log level, then there is no information about individual test cases in the XML file.  
  
I should be able to use boost test messages for tracing and still obtain a compact test summary in xml when needed.  
Adding every message to xml output is in most cases useless for me.  
  
Possible fix:  
Introduce BOOST_TRACE_MESSAGE or move MESSAGE level a few levels up.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-08 20:58:40 UTC  
> Updated at: 2020-04-08 20:59:29 UTC  
> Url: https://github.com/boostorg/test/issues/257#issuecomment-611191192  

If I understand your request, the final XML does not contain the test hierarchy when you use the [log level](https://www.boost.org/doc/libs/1_72_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/log_level.html) above `message`, in order to discard the `BOOST_TEST_MESSAGE` content. Is that correct?  
  
I am not fond of introducing a new switch or functionality. However, have you considered [JUNIT](https://www.boost.org/doc/libs/1_72_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/logger.html) output of Boost.Test? Jenkins has great support for that and that should make things work out of the box.

---

## Comment 2

> Username: kostja  
> Created at: 2020-04-10 17:35:23 UTC  
> Updated at: 2020-04-10 17:36:17 UTC  
> Url: https://github.com/boostorg/test/issues/257#issuecomment-612136342  

Yes, this was precisely my request - so that when running under jenkins it's easy to discard MESSAGE logging.  
We're using junit output. But with millions of messages in the output, the output becomes too heavy for jenkins to handle.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-04-10 17:39:25 UTC  
> Url: https://github.com/boostorg/test/issues/257#issuecomment-612137949  

Thank you for your answer. I still do not understand the problem then, you said  
  
> If I reduce the log level, then there is no information about individual test cases in the XML file.  
  
With JUNIT output, you should be able to raise the level to `warning`, then all `message`s should be discarded. Would you please tell me what is not working with this approach, possibly with screenshots or XML outputs of what you want to keep in your logs and what you don't want.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-05-07 22:56:36 UTC  
> Url: https://github.com/boostorg/test/issues/257#issuecomment-625537924  

Kind reminder @kostja

---

## Comment 5

> Username: kostja  
> Created at: 2020-05-07 23:02:29 UTC  
> Url: https://github.com/boostorg/test/issues/257#issuecomment-625539792  

Will get back to you within a few days, sorry.
