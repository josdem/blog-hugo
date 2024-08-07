+++
title = "Refactoring"
date = 2024-07-29T11:49:01-04:00
categories = ["techtalk", "code","refactoring"]
tags = ["josdem", "techtalks", "programming", "technology","clean code", "refactoring"]
description = "Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure. It is a disciplined way to clean up code that minimizes the chances of introduce bugs. In essence when you refactor you are improving the design of the code after it has been written."
+++

**What is refactor?**

* Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure.
* It is a disciplined way to clean up code that minimizes the chances of introduce bugs.
* In essence when you refactor you are improving the design of the code after it has been written.

**Why do I need refactoring?**

* Programs that are hard to read are hard to modify
* Programs that have duplicated logic are hard to modify
* Programs with complex conditional logic are hard to modify

*Martin Fowler’s reasons*

* The only way to go fast in software development is to keep your code clean
* We are authors
* In software, 80% or more of what we do is quaintly called "maintenance"

*Robert C. Martin’s reasons*

**When I should do refactoring?**

* Refactor when you add function
* Refactor when you need to fix a bug
* Refactor when you do a code review

**What do I need to refactor?**

* A solid test cases
* Be a self-organized programmer
* Trust in tools that can help you

**When I should not refactor?**

* When you do not have sufficient test coverage
* When you spend too much time adding functionality
* When is really difficult to test the code
* When any change causes failures in unexpected ways

**Bad smells in code**

*If stinks, change it*
You will have to develop you own sense of how many instance variables are too many instance variables and how many lines of code in a method are too many lines

## Examples
* [Duplicated Code](/techtalk/refactoring/duplicated_code)
* [Replace Temp With Query](/techtalk/refactoring/replace_temp_with_query)
* [Introduce Parameter Object](/techtalk/refactoring/introduce_parameter_object)
* [Replace Error Code with Exception](/techtalk/refactoring/replace_error_code_with_exception)
* [Replace conditional with polymorphism](/techtalk/refactoring/replace_conditional_with_polymorphism)

[Return to the main article](/)

