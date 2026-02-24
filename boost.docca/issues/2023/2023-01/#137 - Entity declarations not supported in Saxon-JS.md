# #137 - Entity declarations not supported in Saxon-JS [Closed]

> Username: evanlenz  
> Created at: 2023-01-24 01:23:16 UTC  
> Updated at: 2023-01-31 05:45:21 UTC  
> Closed at: 2023-01-31 05:45:21 UTC  
> Url: https://github.com/boostorg/docca/issues/137  

Specifically, on NodeJS (on which Antora, the basis for an upcoming docca POC, is based), a couple of the docca stylesheets make use of entity declarations in the internal DTD subset, and this causes an error in Saxon-JS. I filed a bug with Saxonica (https://saxonica.plan.io/issues/5830), but the answer suggests that it won't get fixed any time soon, since it's reliant on a third-party XML parser, and there appears not to be any fully conformant, non-validating parsers available for NodeJS.  
  
We should refactor these stylesheets to no longer use entity declarations, for better interoperability across all environments.
