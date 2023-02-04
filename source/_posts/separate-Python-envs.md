---
layout: post
title: Keep your Python environment clean with virtualenv
description: Short guide how to install and use virtualenv
tags: [python, virtualenv, virtual environments, dev, devops]
categories: Python
date: 2021-03-23
---

As Cloud Engineer I am working with Python and from time to time scripts/apps can require different Python versions or use many different dependencies. This can lead to some mess if I would like to do all those Python things on my OS. But have no fear - this is where virtualenv comes into play. Thanks to that neat tool we can create isolated Python environments for our projects/applications without messing with each project environment. Installation and usage are VERY simple so let's go through it, shall we?

# Installation
This couldnt be easier:
```pip install virtualenv```

# Creating virtualenv
Now we need directory for our virtualenvs, it can be anywhere but i prefer to have one dir with all my virtual environments so in my example:  
`mkdir venvs`  
`cd venvs`   
Next create environment  
`virtualenv NAME_OF_ENVIRONMENT`

# Usage
now everything we need is just to activate our virtual environment by
`source NAME_OF_ENVIRONMENT/bin/activate`  
i would also recommend to add it as alias in your .zshrc for example:
`alias py3="source ~/venvs/Python3/bin/activate"`
so next time you activate it just by calling `py3` for example.     
If you activated virtualenv you should see in your terminal prompt name of that venv and that mean you are good to go!   
Now any package installed with pip (`pip install PACKAGE_NAME`) is hold in that virtual environment 
without messing anything directly on you OS.

Last thing that will be needed is deactivating your virtualenv which couldn't be simpler:  
`deactivate` - after writing this in terminal you will exit from virtual environment.

That's basically all, i hope this short "how to" was useful and make your work easier.  
See you next time!



