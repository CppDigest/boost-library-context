# #1213 - Review 2: financial example docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:19:44 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-13 15:45:08 UTC  
> Url: https://github.com/boostorg/decimal/issues/1213  

Form the review:  
  
#### Simple Moving Average  
  
Could we link the `moving_average.cpp` to the actual file on github?  
  
Please move the file reading into its own function.  In the current  
implementation `main` keeps the file open during calculating the average.  
  
Please add text that explains what this example demonstrates and why is it  
(better) using decimal floating point than binary.  I understand that it shows  
how to calculate a moving average, but *why* do we want to show that?  What is  
that I need to look for in this example?  What is "its point"?  
  
### Currency Conversion  
  
Could we link `currency_conversion.cpp` to the file on github?  
  
I'd recommend direct `using` declarations not `using namespace` in examples.  
  
I'd use a user defined literal for the value of `exact_eur_amount`, people are  
more used to seeing currency amounts in decimal form, like 920.46EUR.  
  
Please add text that explains what this example demonstrates and why is it  
(better) using decimal floating point than binary.  I understand that it shows  
calculating currency conversions, but *why* do we want to show that?  What is  
that I need to look for in this example?  What is "its point"?  
  
### Boost.Math Integration  
  
Could we link `statistics.cpp` to the file on github?  
  
Same comment about keeping the file open while doing the calculations.  
  
I don't get what the exit code of this program means.  `main` returns  
`mean_closing_price > median_closing_price` but I am not a financial programmer  
(or a statistical one) so I have no clue what that means.  What question does  
that answer?  What does it mean if the mean is larger than the median?  No pun  
intended.  
  
Please add text that explains what this example demonstrates and why is it  
(better) using decimal floating point than binary.  I understand that it shows  
how to use Boost.Math, but *why* do we want to show that?  What is that I need  
to look for in this example?  What is "its point"?
