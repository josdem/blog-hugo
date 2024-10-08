+++
date = 2024-07-31T07:23:55-04:00
title = "Python"
description = "Python is a high-level, general-purpose programming language. Its design philosophy emphasizes code readability with the use of significant indentation"
tags = ["josdem", "techtalks","programming","technology"]
categories = ["techtalk", "code", "python"]
+++

**Why do people use Python?**

* Software quality (readable, reusable and maintainable)
* Developer productivity (is typically one-third to one-fifth the size of equivalent C++ or Java code)
* Program portability (Most Python programs run unchanged on all major computer platforms)
* Support libraries (Python comes with a large collection of prebuilt and portable functionality)
* Component integration (Python scripts can easily communicate with other parts of an application)
* Enjoyment (Easy to built-in toolset)

**Is Python a "Scripting Language"**

Python is a general-purspose programming language that is often applied in scripting roles. It is commonly defined as an object-oriented scripting language- a definition that blends support for OOP with and overall orientation toward scripting roles.

**What are Python's technical strengths?**

* It's Object-Oriented and Functional
* It's free (To use and distribute)
* It's open source
* It's portable
* It's powerful
* It has a large collection of precoded library tools
* It's mixable (Ex. Python's C API lets C programs call and be called by Python)

**First Script**

```python
import sys
print(sys.platform)
print(2 ** 100)
x = 'spam!'
print(8 * x)
```

In this code we are:

* Loading sys library
* raise 2 to a 100 power
* Uses x to string repetition

To run this script type in your terminal:

```bash
python script1.py
```

**Module's files**

To exemplify this create the folloing file: `myfile.py` and add this line:

```python
title='The Meaning of Life'
```

Then write a new file called: `use_myfile.py` and add this line:

```python
import myfile
print('The secret is: ' + myfile.title)
```

Finally run your `use_myfile.py`:

```bash
python use_myfile.py
```

When this file is imported, its code is run to generate the module's attribute. That is, the assignment statement creates a variable and module attribute named title.


[Return to the main article](/)
