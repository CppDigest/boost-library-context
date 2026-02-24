# #87 - Severity filtering is broken when initializing with settings [Closed]

> Username: BrainStone  
> Created at: 2019-06-25 08:32:06 UTC  
> Updated at: 2019-06-25 17:15:06 UTC  
> Closed at: 2019-06-25 17:15:06 UTC  
> Url: https://github.com/boostorg/log/issues/87  

When initializing the logging with `boost::log::settings` as seen [here](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/detailed/utilities.html#log.detailed.utilities.setup.settings) the severity filtering doesn't work. It's also not mentioned that the common attributes and the severity need to be set up sepeartely like this:  
  
```  
    boost::log::add_common_attributes();  
    boost::log::register_simple_formatter_factory< boost::log::trivial::severity_level, char >("Severity");  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-06-25 11:33:07 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505404638  

The page you linked to describes library setup from settings. Initializing from settings does not add any attributes, at all, and that is intentional. The settings are supposed to configure logging behavior of the application, and attributes are part of the application implementation. In other words, attributes are the data that your application exports and the settings configure how that data is presented to the user.  
  
The default filter and formatter parsers do not support any custom attribute value types, you have to register factories with the corresponding `register_*` calls. This is also true with regard to `boost::log::trivial::severity_level`. Although maybe I could add support for it by default.

---

## Comment 2

> Username: BrainStone  
> Created at: 2019-06-25 13:15:59 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505439648  

The thing is the example there seems to be a complete example. And compiling it as is, just doesn't work. So adding these things to said example would be beneficial.

---

## Comment 3

> Username: Lastique  
> Created at: 2019-06-25 14:00:10 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505458037  

It does work as intended. The example only shows how to initialize the library from settings. It doesn't show how to do many other things, like how to create loggers, use attributes and what not. Adding all those things would clutter the example. But I can add a note saying that there may be more things to do before using the library.

---

## Comment 4

> Username: BrainStone  
> Created at: 2019-06-25 14:10:39 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505462603  

I mean have you run the code?    
Since the attributes aren't registered the placeholders print empty strings (if you disable the filters) and the Sevrity also isn't registered, which means nothing would be printed whatsoever, since the filter blocks everything.  
  
The example as it is suggests it is a complete example, meaning you can use it as is. I've talked to several people and none even considered that this might not even be an incomplete example, but rather a broken one.  
  
So I'd urge you to not add a note, but to complete the example, so it does what it seems to be doing.    
While the full example doesn't need to be right there, a full example should at least be linked, so it is clear what additional setup is required.    
Keep in mind many people really just want a working logger and don't have the time to sift through convoluted docs and learn the library internals.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-06-25 14:28:58 UTC  
> Updated at: 2019-06-25 14:32:24 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505470640  

> I mean have you run the code?  
  
As I said, the code does what is intended. Specifically, it shows how to work with settings container and to initialize the library from it.  
  
I don't see where it suggests that the code sample is the complete library initialization. Please, point me to the specific wording that gives that impression and I will try to fix it.  
  
> the Sevrity also isn't registered  
  
The library does not mandate any particular type for severity levels, so in most examples you won't find type registration. This is described in a [separate section](https://www.boost.org/doc/libs/1_70_0/libs/log/doc/html/log/extension/settings.html) at length. This was a deliberate choice to keep the docs more concise and unassuming. The docs are huge as they are already.  
  
There are two examples regarding initialization from settings in log/example: settings_file and settings_file_formatter_factory. They don't show working with the settings container directly, so maybe I'll have to add a separate example showing that.

---

## Comment 6

> Username: BrainStone  
> Created at: 2019-06-25 14:31:57 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505471994  

The fact that it as is, can be compiled and ran suggests it is a complete example.  
  
Linking an actual complete example would be beneficial to the users that just want it to work and make it clear that it isn't.

---

## Comment 7

> Username: Lastique  
> Created at: 2019-06-25 14:34:19 UTC  
> Url: https://github.com/boostorg/log/issues/87#issuecomment-505473051  

> The fact that it as is, can be compiled and ran suggests it is a complete example.  
  
Well, sorry, but that is a wrong assumption to make.  
  
I'll see about adding a complete example. Thanks.
