# #78 - Heading for static member functions incorrect [Closed]

> Username: vinniefalco  
> Created at: 2021-09-08 17:21:16 UTC  
> Updated at: 2021-09-17 23:14:44 UTC  
> Closed at: 2021-09-17 23:14:44 UTC  
> Url: https://github.com/boostorg/docca/issues/78  

The heading for static member functions reads "Static Members". It should read "Static Member Functions." The heading for static data members reads "Static Members" which is correct. Currently they are both the same, which leads to confusion:  
![image](https://user-images.githubusercontent.com/1503976/132555377-6d2119ad-dad6-4d0d-b673-db6d22b28b4d.png)

---

## Comment 1

> Username: evanlenz  
> Created at: 2021-09-17 00:13:44 UTC  
> Url: https://github.com/boostorg/docca/issues/78#issuecomment-921348723  

Here's the full list of allowed values (according to [compound.xsd](https://github.com/doxygen/doxygen/blob/master/templates/xml/compound.xsd)) along with the partial support currently provided by docca (based on my manual attempt at predicting the results of the [current XSLT rules](https://github.com/boostorg/docca/blob/develop/include/docca/base-stage1.xsl#L242)):  
  
sectiondef/@kind | Resulting heading  
-----------|-------  
user-defined |  
public-type | Types  
public-func | Member Functions  
public-attrib | Data Members  
public-slot |  
signal |  
dcop-func |  
property |  
event |  
public-static-func | Static Members  
public-static-attrib | Static Members  
protected-type |  
protected-func | Protected Member Functions  
protected-attrib | Protected Data Members  
protected-slot |  
protected-static-func | Protected Static Members  
protected-static-attrib | Protected Static Members  
package-type |  
package-func |  
package-attrib |  
package-static-func |  
package-static-attrib |  
private-type |  
private-func | Private Member Functions  
private-attrib | Private Data Members  
private-slot |  
private-static-func | Private Static Members  
private-static-attrib | Private Static Members  
friend | Friends  
related | Related Functions  
define |  
prototype |  
typedef |  
enum | Values  
func |  
var |  
  
It sounds like, at minimum, we should adjust the rules so that any value that ends with `static-func` should result in a heading that ends with "Static Member Functions" like so:  
  
kind | Heading  
----|----  
public-static-func | ~~Static Members~~Static Member Functions  
protected-static-func | ~~Protected Static Members~~Protected Static Member Functions  
private-static-func | ~~Private Static Members~~Private Static Member Functions  
  
@vinniefalco Do you see any other obvious corrections (or additions!) we should make? Feel free to fill in the rest of the above table with whatever headings you want to add support for, and I'll make it happen in the XSLT. If you're not sure what a value means, it's probably fine to leave it alone for now.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-09-17 00:17:01 UTC  
> Url: https://github.com/boostorg/docca/issues/78#issuecomment-921350377  

protected-type: "Protected Types"  
private-type: "Private Types"  
  
slot and package are used when extracting features from languages other than C++. Thanks!
