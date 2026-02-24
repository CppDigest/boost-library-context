# #123 - boost review summary by Mateusz Loskot [Closed]

> Username: HDembinski  
> Created at: 2018-10-22 12:25:42 UTC  
> Updated at: 2019-02-05 12:56:20 UTC  
> Closed at: 2019-02-05 12:56:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/123  

Hi Everyone,  
  
Please, welcome the Histogram library to the Boost collection!  
  
Thank you Hans for submitting a high-quality library for review.  
  
Thank you to the reviewers as well as those who just exchanged  
their comments on numerous aspects of the library  
and participated in the fruitful discussions.  
  
Among reviewers, we can recognise veteran Boost contributors and  
authors as well as experts in the fields of statistics, raster analysis,  
including authors of histogram-oriented software packages.  
  
----------------------------------------  
Official reviews and votes:  
----------------------------------------  
  
1. Klemens David Morgenstern - ACCEPT  
2. Jim Pivarski (off-list) - ACCEPT  
3. Bjorn Reese - ACCEPT conditionally  
4. Steven Watanabe - ACCEPT conditionally  
5. Alex Hagen-Zanker - ACCEPT  
  
Non-review participations:  
  
1. Andrea Bocci (off-list)  
2. degski  
3. Glen Fernandes (off-list)  
4. Gavin Lambert  
5. Seth  
6. Mateusz Loskot  
  
----------------------------------------  
Acceptance conditions  
----------------------------------------  
  
Two reviews stated conditions which are important fulfilled:  
  
Bjorn Reese  
- Reference documentation is finalized  
  
Steven Watanabe  
- Various exception safety and memory issues must be fixed.  
- The reference documentation must be completed. Specifically,  
 various preconditions, requirements,  
 and assumptions must be made explicit.  
  
I believe those are not controversial at all and  
Hans is also interested in addressing those requests.  
  
----------------------------------------  
Concerns and comments  
----------------------------------------  
  
Jim Pivarski mentioned importance of profile plots as fundamental enough  
that they should be part of the feature set.  
In short term, the profile plots can be implemented as a new storage type,  
using the extension capabilities.  
Similarly, some concurrent applications would need atomic-valued bins,  
but these, too, can be storage types.  
  
Bjorn Reese pointed out some aspects of the design that makes the library  
not work very well with STL algorithms. Bjorn strongly recommended that  
the default storage policy is changed to remove some of those limitations.  
  
Steven Watanabe provided Hans with large number of very detailed comments  
and fixes (eg. do not call destructor which has been fixed),  
suggestions how to improve run-fail tests and serialization tests.  
  
Alex Hagen-Zanker commented that range-based for-loops support could  
be improved,  
suggested to provide separate ranges or views for certain histogram  
access scenarios.  
Another important theme of Alex' review was Histogram applicability for  
moving window type analysis. Then, Alex requested clarification on choosing  
variance based on the Poisson distribution instead of the binomial distribution.  
  
degski suggested to look for some "low hanging fruits" in C++14 and/or C++17,  
the library could potentially benefit from, pushing the 'modern C++'  
trait of the library even further.  
  
Gavin Lambert suggested to allow use of quantity types like Boost.Units.  
  
Glen Fernandes mentioned he would raise allocator model support issues  
or relaxing the requirement that the pointer must be a raw pointer of  
type value_type*. Glen mentioned he may even be available to help.  
  
Hans has already addressed many of the suggestions and issues or planned  
related features for future releases of the library.  
Issues resulting from the feedback received during the review can be  
tracked and further discussed on GitHub, labelled with boost-review:  
  
https://github.com/HDembinski/histogram/labels/boost-review  
  
----------------------------------------  
The Upsides  
----------------------------------------  
  
There were lots of great things said about the Histogram library:  
  
- design: excellent, clean, simple and extensible in principle  
- useful and well-designed features  
- geared towards readability and performance  
- very good documentation  
  
----------------------------------------  
Final Thoughts  
----------------------------------------  
  
Reviewers are worth their weight in gold nowadays.  
Once again, I'd like to thank all participants for great  
amount of quality feedback that already led to important  
improvements in the Histogram library in very timely manner  
- thank you Hans!  
  
The Histogram library is a valuable addition to Boost.  
_______________________________________________  
Unsubscribe & other changes: http://lists.boost.org/mailman/listinfo.cgi/boost

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-02-05 12:56:20 UTC  
> Url: https://github.com/boostorg/histogram/issues/123#issuecomment-460627896  

closing this, as there are other tickets which address the remaining open issues
