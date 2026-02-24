# #137 - Documentation comments from Peter Turcan [Closed]

> Username: mborland  
> Created at: 2024-01-24 07:04:38 UTC  
> Updated at: 2024-01-30 07:55:46 UTC  
> Closed at: 2024-01-30 07:55:46 UTC  
> Url: https://github.com/boostorg/charconv/issues/137  

Charconv Library Documentation review  
================================  
  
The documentation is a good start.  
  
Main issues:  
1. Needs a full overview.  
2. Seems like an API Reference section should be organized, combining the  
two from_chars/to_chars sections into one, with API standard type sections  
and subheadings.  
  
Other issues:  
1. Make code/sections clearer with introductory sentences.  
2. Use tables for tabular data, rather than bulleted lists.  
3. All mentions of an API element should link to the page/section on that  
element.  
4. Other notes and suggestions below.  
  
  
OVERVIEW:  
  
There are three questions to be answered with API docs: What, Why, How - in  
that order. The Why question is the most important and usually the most  
neglected.  
  
The "What" question ("What makes up the Boost.Charconv library"). For  
example: "Boost.Charconv converts character buffers to numbers, and numbers  
to character buffers. It is a small library of two overloaded functions to  
do the heavy lifting, plus several supporting enums, structures, templates,  
and constants, with a particular focus on performance and consistency  
across the supported development environments."  
  
The overview really needs to answer the "Why" question: "Why should I [a  
C++ developer new to Boost] be interested in this library?". There should  
be several paragraphs explaining how it compares with the standard library  
and why use this one instead (or as well as perhaps?), and what scenarios  
the library is most suited for. *A developer who knows nothing about the  
library should know at the end of the overview whether this is for them or  
not.*  
  
If a claim is made, such as "non-allocating", this should be explained with  
at least one sentence (no use of malloc perhaps?), and there should be a  
note of whether there are exceptions. If there are one or two exceptions  
then they can be mentioned by name in the overview. If there are more, add  
a sentence that there are exceptions to a general rule, and instead link to  
a section listing and explaining those differences.  
  
Is the charconv standard library the opposite: locale-dependent,  
allocating, or throwing - if so point that out.  
  
The overview should be upbeat in tone, and should clearly mention  
performance.  
  
Dependencies  
  
It is good practice to mention any significant dependencies - on Boost or  
other libraries - as part of the overview or part of the initial setup  
process. It is good practice too to mention that there are no significant  
dependencies.  
  
The rest of the doc should answer the final question, the "How" question:  
  
Usage Examples  
  
Add a sentence stating what the examples do (before each and every code  
block), such as "The following examples show a straightforward use of the  
two core functions of the library, from_chars and to_chars, converting a  
character string to a number (integer or floating point) and converting a  
number to a character string. Note that the character string buffer can be  
of any length."  
  
Supported Compilers  
  
Good - clear. What about supported Operating Systems?  
What about unsupported compilers - does that mean the lib will not work, or  
that the lib has not been tested on other compilers? If the latter, perhaps  
suggest that it is up to the developer to verify if the lib works correctly  
on an unsupported compiler? Would running the benchmarks on an unsupported  
compiler do the trick?  
  
  
Why use Boost.Charconv over <charconv>?  
  
Move this up to the Overview with more details - change "several times  
faster" to some specific examples. For example using the  
Boost.Charconv:from_chars_result function with floating point numbers can  
work from 3.23 to 5.77 times faster than the standard library. OK to link  
to your benchmarks for more details, but don't require it to answer the Why  
question. And are there any areas where this library performs better  
generally - floating point numbers, long integers, anything else - then  
mention this in the Overview.  
  
Building the Library (perhaps rename to "Getting Started" as it involves  
more than just building)  
  
Introduce the section, start with a sentence such as: "The following  
section explains how to download and build this library locally on your  
development machine."  
  
B2  
What if the developer wants to use C++13, does that mean cxxstd=13 should  
be set, or should it always be set to 11. Explain any options.  
  
vcpkg  
Change "Any required Boost packages that do not already exist will be  
installed automatically." to  
"Any required Boost packages not currently installed in your development  
environment will be installed automatically."  
  
Add the TIP code into the tip itself, if possible, so its indentation is  
correct.  
  
from_chars  
Add a general title before this something like "API Reference" with an  
introductory sentence (though see the notes on Organization below):  
"This section describes all the functions, structures, enums, constants,  
and macros available in the library." This is generally a good order to  
describe an API reference.  
  
Add some text to describe what the source code does or is. Is this the  
source code of the function in the library, or perhaps a summarized version  
of its definition? Perhaps add a subheading "Definition" or "Syntax".  
  
from_chars_result  
The bulleted list of error codes would be better presented as a table with  
sub-heading "Return Values" and two columns entitled: "Value" and  
"Description" (or similar). This format should be re-used by  
to_chars_result.  
  
from_chars [ don't reuse a heading string at the top level] Parameters (or  
from_chars Parameters) might be a better title here, or re-organize as  
suggested below.  
Again, the parameters would be better represented by a table rather than  
bullets, with sub-heading Parameters and columns "Name" and "Description".  
This format should be re-used by to_chars.  
  
"One known exception is GCC 5 which does not support constexpr comparison  
of const char*." - do you have a workaround or suggestion on how to handle  
this?  
  
Consider more descriptive headings for:  
from_chars for integral types  
from_chars for floating point types  
- perhaps  
Usage notes for from_chars with integral types  
Usage notes for from_chars with floating point types  
  
Examples  
Integral, Floating Point, Hexadecimal  
- add a sentence preceding each code block describing what is going on, and  
any particular nuances to be aware of  
Same for the invalid arguments - always add a descriptive sentence  
preceding code blocks explaining what is going on.  
  
to_chars  
- same notes as for from_chars  
  
chars_format  
Add an  intro sentence, noting that this is an enum defining the supported  
number formats.  
  
Limits  
Add an intro sentence describing what is in this section, and that Limits  
are templates (or reorganize as suggested below).  
Same notes as above - explain what the Examples are showing, plus nuances  
  
Reference  
Not sure what is meant by "Reference" as the above sections appear to be  
API reference material. Seems to be a lot of duplication of information  
from the above section? Perhaps consider combining the two sections into a  
single reference?  
  
References can benefit from being divided up into sections:  Functions,  
Structures, Enums, Macros, Constants, etc. This makes it clear (from  
looking at the table of contents) what the complete contents of the lib are.  
  
Benchmarks  
  
Provide a sentence/paragraph describing the purpose of this section.  
Something like:  
"This section describes a range of performance benchmarks that have been  
run comparing this library with the standard library, and how to run your  
own benchmarks if required."  
  
Might need some explanation for libdouble-conversion and {fmt} - such as  
"Download the zip file and install the code to your development project" -  
or whatever steps are necessary.  
  
Like the use of tables. Is this enough information "to_chars floating point  
with the shortest representation" - what is meant by "the shortest  
representation"?  
  
Perhaps make it clearer the order of the numbers in Relative Performance -  
standard charconv first, then Boost.Charconv second? Just to be completely  
clear.  
  
Acknowledgements  
  
Perhaps move "Special thanks to Stephan T. Lavavej for providing the basis  
for the benchmarks." to an Acknowledgements section, so you can add more  
names as appropriate (testers, design feedback, etc.). A bulleted list  
works here.  
  
Sources  
  
Perhaps add an introductory sentence here - not sure that you copied any  
algorithms from these but more inspiration and ideas?  
  
Maybe: "The authors acknowledge the inspiration and guidelines from the  
following published works." - or similar  
  
  
Missing Information?:  
  
What about defined constants, such as BOOST_CHARCONV_RUN_BENCHMARKS,  
BOOST_CHARCONV_CONSTEXPR  - should there be a table of constants defined by  
the library - with an explanation of the use of each?  
  
ORGANIZATION and NAVIGATION  
  
Ideally, I would organize the doc into pages (each  [page] indicates a new  
html page).  And ideally the pages are well linked together, for example,  
each time from_chars_result is mentioned, it is a link to the page for that  
structure. Same for all entries of the functions, structs, enums, etc.  
  
Overview [page]  
  Intro - answer the What and Why questions  
  Supported compilers/OS  
Getting Started [page]  
  Downloading and building  
  Dependencies  
Basic usage examples [page]  
  from_chars examples  
  to_chars examples  
API Reference [page]  
  toc (table of contents: Functions, Structures, Enumerations, Templates,  
Constants, in a table of links)  
Functions [page]  
  toc (table of contents: from_chars, to_chars)  
  from_chars [page]  
    overview  
    definition/syntax (include the library path where the function is  
defined)  
    parameters  
    return value  
    remarks/notes [add lower level headings for integers, floating point,  
hexadecimal etc. as needed]  
    examples  
  to_chars [page]  
    overview  
    definition  
    parameters  
    return value  
    remarks/notes  
    examples  
Structures [page]  
  toc  
  from_chars_result [page]  
    overview  
    definition  
    fields  
    remarks/notes  
  to_chars_result [page]  
    overview  
    definition  
    fields  
    remarks/notes  
Enumerations [page]  
  toc  
  chars_format [page]  
    definition  
    members  
    remarks/notes  
Templates [page]  
  toc  
  Limits [page]  
    overview  
    definition  
    remarks/notes  
Constants [page]  
  table of defined constants with descriptions  
Benchmarks  
  toc  
  How to run...  
  Linux  
  Windows  
  MacOS  
Sources  
Acknowledgements  
  copyright & license
