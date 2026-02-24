# #139 - Type aliases with template parameters are rendered wrong [Open]

> Username: anarthal  
> Created at: 2023-02-24 12:37:10 UTC  
> Updated at: 2023-03-04 20:53:30 UTC  
> Url: https://github.com/boostorg/docca/issues/139  

Example: https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__metadata_collection_view.html  
  
`metadata_collection_view` is defined as a type alias for `boost::span<const metadata>`, where `metadata` is an object defined by MySQL. I'd expect the reference page to only show the type alias, but it's also listing the member functions for `metadata`.  
  
A slightly less clean example: https://www.boost.org/doc/libs/1_81_0/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html  
Where the member functions section contains functions from the `file` class.  
  
If no member functions are defined for the type in the `span`, everything works fine though:  
https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__blob_view.html

---

## Comment 1

> Username: evanlenz  
> Created at: 2023-03-04 07:24:15 UTC  
> Updated at: 2023-03-04 07:27:37 UTC  
> Url: https://github.com/boostorg/docca/issues/139#issuecomment-1454619425  

Hi @anarthal (and @vinniefalco), I can see how the member functions are being included. I will dig into this further, but because C++ isn't my area of expertise, I was wondering if you would like to help me figure out the correct behavior in the general case. I'm including the technical detail below for my own reference and also for you, only if you're interested in digging deeper so as to help me figure out what should and shouldn't be included in the page (again, in the general case). If not, no worries. I still have a next step I will take regardless (see last paragraph below). Either way, we'll get this fixed for you.  
  
The code is including all the info from a referenced typedef class in the data for the page:  
  
* [via this rule in base-extract-xml-pages.xsl](https://github.com/boostorg/docca/blob/develop/include/docca/base-extract-xml-pages.xsl#L314-L324).  
  
You can see the intermediate result of this in your build directory in this file:  
_bin.v2/libs/mysql/doc/\_reference-dir/xml-pages/boost__mysql__metadata_collection_view.xml_  
  
The next stage of processing finalized the content of the page, ignoring the undesired parts of that inclusion:  
  
* [via these rules in base-stage1.xsl](bin.v2/libs/mysql/doc/_reference-dir/xml-pages/boost__mysql__metadata_collection_view.xml  
).  
  
(Line 483 is what causes the included class to be rendered by default, and the subsequent rules make the exceptions.)  
  
You can see the results of that stage here:  
_bin.v2/libs/mysql/doc/\_reference-dir/stage1/results/boost__mysql__metadata_collection_view.xml_  
  
Apparently, it is not ignoring enough!  
  
To try and divine my original intentions for doing this, with some effort, I tracked down the older git history which sadly didn't get copied over to the current project. Here are the two relevant commits where I added this code (almost four years ago!):  
  
* ["Added support for typedef tables (referenced classes)"](https://github.com/evanlenz/beast/commit/8dc2ee82912e0a0f600e673fcdd0e208135938c0)  
* ["Categorize referenced classes to avoid rule collisions"](https://github.com/evanlenz/beast/commit/d2c743e69264f6774edcac13d7e32a14ca5568c7)  
  
My next step will probably be to disable the abovementioned line 483 and see what breaks in the other projects that use docca. (I have some [test scripts](https://github.com/boostorg/docca/tree/develop/util/comparison) that I use and I will now add the mysql project to the list to help guard against future regressions in your project as well!)

---

## Comment 2

> Username: anarthal  
> Created at: 2023-03-04 20:52:58 UTC  
> Updated at: 2023-03-04 20:53:30 UTC  
> Url: https://github.com/boostorg/docca/issues/139#issuecomment-1454871721  

Hi @evanlenz, thanks for looking into it :)  
For [the MySQL page](https://www.boost.org/doc/libs/master/libs/mysql/doc/html/mysql/ref/boost__mysql__metadata_collection_view.html), the correct behavior is as follows:  
* `boost::span<T>` is a class defined by a library external to Boost.MySQL, so we don't have any info on this class.  
* Although `metadata` is defined by MySQL, `boost::span<metadata>` is a container of metadata objects, so it does not inherit any member function, property or description from `metadata`.  
* The description section on top is good, but the member function section should not appear. As an image is usually better than words, I've hand-drawn what should and should not be shown.  
* In the general case, unless I'm missing something, the docs for `class TemplateClass<T>` should not pull anything from the type `T`.  
  
I'm afraid I'm not familiar with docca (and XSL scares me), so I'm afraid I won't be very helpful with diagnosis.  
  
Cheers,  
Ruben.  
  
![Screenshot 2023-03-04 at 21-45-49 metadata_collection_view - master](https://user-images.githubusercontent.com/34971811/222928158-5b640ff3-5a84-4f2a-81d3-da8d6198f4c1.jpg)
