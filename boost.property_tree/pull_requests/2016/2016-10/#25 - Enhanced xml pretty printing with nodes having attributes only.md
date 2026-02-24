# #25 Enhanced xml pretty printing with nodes having attributes only. [Open]

> Username: martin-yum  
> Created at: 2016-10-25 12:52:25 UTC  
> Updated at: 2017-02-19 15:44:54 UTC  
> Url: https://github.com/boostorg/property_tree/pull/25  

Each attribute is printed in a separate, indented line now.  
This makes the xml better readable, especially if there are many attributes in the node.  
  
Signed-off-by: e-driver e-driver@t-online.de

---

## Comment 1

> Username: martin-yum  
> Created_at: 2016-10-25 12:59:32 UTC  
> Url: https://github.com/boostorg/property_tree/pull/25#issuecomment-256027359  

This is a simple improvement for xml export. If you have nodes with many (and probably long) attributes, the xml file becomes very ugly with all attributes in a long line. Now all attributes are tiny in new lines like subnodes. In case of mixed nodes (attributes and subnodes) the behaviour is unchanged (all attributes in one line).

---
