# #402 - Better handling of obs-fold in parser [Closed]

> Username: vinniefalco  
> Created at: 2017-05-31 14:46:07 UTC  
> Updated at: 2017-05-31 14:46:38 UTC  
> Closed at: 2017-05-31 14:46:38 UTC  
> Url: https://github.com/boostorg/beast/issues/402  

Currently the rfc7230 utilities like `ext_list` use an internally allocated `std::string` to handle the case where an HTTP field value contains an obsolete line continuation. We can eliminate the need for this allocation if the parser itself handles the line continuation simply by replacing the CRLF with spaces, or optionally deleting the CRLF and extraneous spaces.  
  
This would also require a memory allocation. The parser already allocates memory for the case where the input buffer sequence is discontiguous, we could use the same buffer.  To satisfy some user desires for a non-allocating implementation, a new parser option can be added which makes any received **obs-fold** illegal, generating an error.  
  
Finally the rfc7230 field value adapters can be modified to no longer need an additional `std::string`.
