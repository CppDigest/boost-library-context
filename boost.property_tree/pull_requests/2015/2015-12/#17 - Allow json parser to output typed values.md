# #17 Allow json parser to output typed values. [Closed]

> Username: jeremycochoy  
> Created at: 2015-12-21 04:33:26 UTC  
> Updated at: 2016-02-10 13:01:42 UTC  
> Closed at: 2016-02-10 13:01:42 UTC  
> Url: https://github.com/boostorg/property_tree/pull/17  

Hi,  
  
This is a short patch that allow writing typed value in json, a "bug" issued more than 4 years ago (see http://stackoverflow.com/questions/2855741/why-boost-property-tree-write-json-saves-everything-as-string-is-it-possible-to ) .  
  
Feel free to patch the patch as much as you want.  
  
Nb : This patch isn't perfect. Since ptree doesn't store data type, all typed values wrapped around string will be written typed, and there is no way to prevent that. But it's still nicer than storing everything in strings.  
  
Regards,  
  
Edit : I added a patch asked 6 years ago : https://svn.boost.org/trac/boost/ticket/3828 Removing a trailing std::endl line.

---

## Comment 1

> Username: CornedBee  
> Created_at: 2016-02-10 13:01:42 UTC  
> Url: https://github.com/boostorg/property_tree/pull/17#issuecomment-182362390  

I'm extremely wary of guessing games. Proper support for typed JSON in property tree is my next big goal, but I don't like having strings in the property tree and then guessing whether they are something else. The goal is to rewrite the JSON serializer completely.

---
