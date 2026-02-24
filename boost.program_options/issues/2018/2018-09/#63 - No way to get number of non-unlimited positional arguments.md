# #63 - No way to get number of non-unlimited positional arguments [Open]

> Username: mossheim  
> Created at: 2018-09-13 04:05:47 UTC  
> Updated at: 2018-09-13 04:05:47 UTC  
> Url: https://github.com/boostorg/program_options/issues/63  

[Documentation](https://www.boost.org/doc/libs/1_68_0/doc/html/boost/program_options/positiona_1_3_31_9_9_1_1_1.html) for `positional_options_description` says it has a `max_total_count` function that returns the number of positional arguments, and may return `numeric_limits::max` to indicate unlimited arguments. However, there is no way to determine the number of counted arguments.  
  
Given an instance of `positional_options_description`, it is therefore impossible, for example, to print out a list of all positional arguments, stopping at the last, unlimited argument. A new member function that provides this is all you'd need to add, IMO.
