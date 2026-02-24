# #226 - Please consider improving the documentation and examples [Closed]

> Username: chibicitiberiu  
> Created at: 2024-03-02 01:58:23 UTC  
> Updated at: 2024-03-02 21:27:48 UTC  
> Closed at: 2024-03-02 21:27:48 UTC  
> Url: https://github.com/boostorg/log/issues/226  

I was considering using this library for a personal project. While trying to set up a basic project, I attempted to set up a configuration file with 2 sinks (console and a file). It is not working, and I have no idea why. I am not getting any log output, I am not getting any exception, the file I wrote the logs to doesn't exist. I am lost, and after wasting hours on this, I'm just done with Boost.Log.  
  
I was very frustrated about how bad the documentation is:  
  
* in most code examples, it is not mentioned what include files to use. I ended up having to search the source code of the library to see where things like "BOOST_LOG" are defined. Consider using a tool like doxygen to autogenerate API documentation.  
* some things are just wrong and misleading. While reading the [tutorial page on sources](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/tutorial/sources.html), it says to use `src::logger lg;`. It took me a while searching the sources to figure out that the namespace `src` isn't correct, the correct one is `boost::log::sources`.  
  
Using a settings file was not documented at all. I had to figure out how to do it based on the [example code](https://github.com/boostorg/log/blob/develop/example/settings_file/main.cpp). There are a lot of strange things in this example that make no sense to me, as I am unfamiliar with the library and its APIs:  
  
```C++  
        // Add some attributes  
        logging::core::get()->add_global_attribute("TimeStamp", attrs::local_clock());  
```  
What does this do? How is it relevant to reading from a settings file? Please don't make me have to copy the example and try to run it myself to figure out.  
  
```C++  
        // Now enable tagging and try again  
        BOOST_LOG_SCOPED_THREAD_TAG("Tag", "TAGGED");  
```  
Again, what does this do? What is tagging? And how is it relevant to reading from a settings file?  
  
Trying to understand this example, I started reading [the documentation on attributes](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/detailed/attributes.html), the first paragraph is:  
  
> All attributes in the library are implemented using the [pimpl idiom](http://c2.com/cgi/wiki?PimplIdiom), or more specifically - shared pimpl idiom. Every [attribute](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/boost/log/attribute.html) provides an interface class which derives from the attribute class and an [impl](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/boost/log/attribute/impl.html)ementation class that derives from impl. The interface class only holds a reference counted pointer to the actual implementation of the [attribute](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/boost/log/attribute.html); this pointer is a member of the [attribute](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/boost/log/attribute.html) class, so derived interface classes don't have any data members. When the interface class is default constructed, it creates the corresponding implementation object and initializes the attribute base class with a pointer to the implementation. Therefore the pimpl nature of attributes is transparent for users in a typical workflow.  
  
What the heck is pimpl, and why do I care? Oh, even better, it's not pimpl, it's shared pimpl. Great to know.  
  
I'm here to understand how the library works. This whole paragraph is completely irrelevant and doesn't explain anything.  
  
I held hope that the link to the [attribute](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/boost/log/attribute.html) class would help, but this is doesn't explain it either:  
  
> Every attribute is represented with a factory that is basically an attribute value generator. The sole purpose of an attribute is to return an actual value when requested. A simplest attribute can always return the same value that it stores internally, but more complex ones can perform a considerable amount of work to return a value, and the returned values may differ each time requested.  
  
Again, no explanation on what it is.  
  
Reading further in the "attributes" page:  
  
> The main purpose of attributes is to generate attribute values. Values are semantically distinct from the attributes. Such separation allows implementing attributes that can return different values at different time points (like clock-related attributes, for example) and, on the other hand, allows using different values of the same attribute independently.   
  
Here, I was thinking "yes, finally, an explanation of that these things are"... and then it just completely fails again to explain what they are. What does it mean to "generate attribute values"? What are those? Why do I need them?  
  
----  
  
These were just a few examples I found, but I hope you understand my point. The documentation leaves a lot to be desired, and fails to explain to someone completely new how the library works, what the concepts are.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-03-02 11:42:39 UTC  
> Updated at: 2024-03-02 11:43:04 UTC  
> Url: https://github.com/boostorg/log/issues/226#issuecomment-1974773384  

> in most code examples, it is not mentioned what include files to use.  
  
There is a link to the complete source code of every non-trivial code example below the example. The relevant headers are also listed at the beginning of every section, and those headers are also links to Reference documentation.  
  
Can you be more specific which examples are lacking this information?  
  
> Consider using a tool like doxygen to autogenerate API documentation.  
  
Doxygen *is* used to generate Reference documentation.  
  
> It took me a while searching the sources to figure out that the namespace `src` isn't correct, the correct one is `boost::log::sources`.  
  
It's not incorrect, the namespace shorthands used in the documentation are defined [here](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/how_to_read.html). The shorthands are used to reduce syntactical clutter in the code samples.  
  
I could probably improve exposure of this information by moving it to the front page. This would require restructuring docs though.  
  
> Using a settings file was not documented at all.  
  
[It is](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.settings_file).  
  
> I had to figure out how to do it based on the example code. There are a lot of strange things in this example that make no sense to me, as I am unfamiliar with the library and its APIs  
  
The example demonstrates how to load the settings file and how adding attributes affect it. All examples are there to demonstrate this or the other feature or use case of the library, and users are free (and even expected) to compile and experiment with examples and see how the library behaves. Understanding what each line does requires one to read the documentation before that, though.  
  
> What the heck is pimpl  
  
The term is the link right there in the text.  
  
> and why do I care?  
  
Because it affects how attributes behave. It is also important to know if you're going to implement your own attribute. This information is literally what you're asking:  
  
> I'm here to understand how the library works.  
  
> Here, I was thinking "yes, finally, an explanation of that these things are"... and then it just completely fails again to explain what they are. What does it mean to "generate attribute values"? What are those? Why do I need them?  
  
There is a section with [basic term definitions](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/defs.html) as well as the [design overview](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/design.html) which are recommended to be read first in the [How to read](https://www.boost.org/doc/libs/1_84_0/libs/log/doc/html/log/how_to_read.html) section I mentioned above. These sections give you understanding of how the library operates as a whole. With that understanding, it should be easier to understand the details given in the Detailed feature description.  
  
I can give a few introductory words in the Attributes section that basically duplicate what is written on the pages I linked, but this will be just that - duplication, and it will not relieve the reader from reading those other pages anyway as they are necessary to understand how the library works.  
  
In any case, thank you for your feedback.
