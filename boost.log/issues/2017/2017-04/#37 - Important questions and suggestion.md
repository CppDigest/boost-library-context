# #37 - Important questions and suggestion [Closed]

> Username: BeyondW  
> Created at: 2017-04-04 05:39:23 UTC  
> Updated at: 2020-08-24 17:43:29 UTC  
> Closed at: 2017-04-04 10:09:10 UTC  
> Url: https://github.com/boostorg/log/issues/37  

I have been studying the library for 2 weeks resulting in considerable disappointment both in the readability of documents and inconvenience of the lib.  Here are some inquiry and suggestion.  
1. `expr::format_date_time<boost::posix_time::ptime>("TimeStamp", "%Y-%m-%d %H:%M:%S")` could not be used with lambda style formatter which is not illustrated. I suggest the document should give a clear example to show how to formatting TimeStamp in lambda style formatter and place the most notably marked notification.  
	  
2.The relationship of the headers are complex. In the tutorial, I strongly recommend to indicate which part each header is responsible for.  
  
3.A very important requirement in log system is not given an example in the tutorial. How can I assign different loggers to different sinks or streams added to the sink backend? For example:  
I have 3 modules(A B C) in my system. My requirement is all information of A are printed in the console, all info of B logged in B.log and all info of C logged in C.log.   
  
I wish the document could be designed more friendly and with more consideration in practical user scenario. Thanks for any response.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-04-04 10:09:10 UTC  
> Url: https://github.com/boostorg/log/issues/37#issuecomment-291455478  

1. I'm not sure what you mean by "using it with lambda style formatter". `format_date_time` can be used in the Boost.Phoenix lambda expression, an example is given [here](http://www.boost.org/doc/libs/1_63_0/libs/log/doc/html/log/detailed/expressions.html#log.detailed.expressions.formatters.date_time). The formatter is not designed to be called directly from a C++11 lambda, if that is what you want, because it has to cache the parsed format string. But you can work it around by injecting your C++ lambda into the Boost.Phoenix expression.  
  
I cannot document every formatting or filtering tool in the tutorial because it has to be short and just get you started. It is not meant to give you the full overview of the library. All components are documented in a separate [section](http://www.boost.org/doc/libs/1_63_0/libs/log/doc/html/log/detailed.html).  
  
2. There is a link to the complete code by every example in the tutorial. Includes are omitted from the examples in the tutorial for brevity. There are links to the related headers in the beginning of every section in the detailed description of every component.  
  
3. This is done by filtering, which is covered in the tutorial. There is a good summary of how to do this in [this](http://stackoverflow.com/questions/34362871/use-channel-hiearchy-of-boost-log-for-severity-and-sink-filtering/34372526#34372526) SO answer.  
  
I know the tutorial is probably not delivering in the full library overview department, but the library is too big to put everything in the tutorial. There are many use cases, I can't describe every one of them. Instead, I tried to document every component separately and the library design so that users are able to choose the components they need and combine them.  
  
That said, patches to improve the docs are always welcome.

---

## Comment 2

> Username: jonesmz  
> Created at: 2020-08-24 09:37:29 UTC  
> Updated at: 2020-08-24 09:37:39 UTC  
> Url: https://github.com/boostorg/log/issues/37#issuecomment-679021013  

> There is a link to the complete code by every example in the tutorial. Includes are omitted from the examples in the tutorial for brevity.  
  
I've been working with Boost log for about 2 years now, and I think this is something that should be avoided in the documentation. I frequently find myself very confused about the intended way to do things, or even if some things are possible. But even more narrowly, having to hunt down where the heck a magic keyword / function / identifier is coming from in the header files is a major pain.  
  
Brevity is not a desirable trait in technical documentation in general, as it tends to leave the reader left without any information of what the relationship between the parts left out of the explanation are, and frequently there are very few relevant results for Boost::Log on google or stack overflow for even question that seem simple.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-08-24 13:15:10 UTC  
> Url: https://github.com/boostorg/log/issues/37#issuecomment-679119172  

If you found the documentation of the particular component in Boost.Log docs, you can see the relevant includes at the very beginning of the section.  
  
Regarding tutorial specifically, I would have liked to have some sort of expandable source code that is hidden by default to allow users to see the includes in-place, but unfortunately this is not possible with QuickBook. Links to full examples will have to do.  
  
PS: You can see in the full source code that the amount of includes sometimes come close to the actual code snippets in terms of line count. I don't want to double the amount of code users have to parse through to get the basic understanding of the library, which is what the tutorial is for.

---

## Comment 4

> Username: jonesmz  
> Created at: 2020-08-24 17:43:29 UTC  
> Url: https://github.com/boostorg/log/issues/37#issuecomment-679270614  

Well, it's your project, so if you think that's a major concern, then go for it.  
  
But I strongly disagree with you. It's really annoying to see examples, from any project, that I can't just copy-paste into a file and compile. When reading the documentation from Boost.Log, i spend more time trying to figure out where in the project source code the items being documented are coming from than I do reading the documentation itself. At that point, why bother with the documentation? Incomplete documentation can sometimes be worse than no documentation, because people all over the internet (e.g. stackoverflow) respond to questions about the topic with hyperlinks to the incomplete documentation that didn't answer the question in the first place, thus necessitating the post on stackoverflow. It's a self-reinforcing problem.  
  
Anyway, that's my thoughts on the issue. I won't bring it up again unless I'm asked to.
