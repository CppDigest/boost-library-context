# #87 - Changing strides APIs to support custom order [Closed]

> Username: amitsingh19975  
> Created at: 2020-06-06 15:22:38 UTC  
> Updated at: 2020-06-06 15:22:49 UTC  
> Closed at: 2020-06-06 15:22:49 UTC  
> Url: https://github.com/boostorg/ublas/issues/87  

Introducing new order `custom_order` which allows the user to add any type of strides other than  `column_major` or `row_major` which makes the `tensor` more modular and gives a new customization point. To incorporate there needs to be a few changes in strides classes.
