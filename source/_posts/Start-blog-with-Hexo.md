---
title: Start blog with Hexo
date: 2020-11-05 19:29:09
tags: [Hexo,blog,setup]
---
Beginning...
===============

I finally decided to start blog to save memories from my IT journey. Don't get me wrong 
i tried to start own blog many times(too many :-D) but this time I'm really doing this!
After some research i decided to go with Hexo because of Node.js and simplicity.
<br/>
I will shortly guide you how to setup Hexo on Github. 
Instruction of pages repository creation can be found at [GitHub](https://pages.github.com/) it is 
easy and well explained so o won't go through it here.
Let's focus on Hexo now:
<br/>

## Install npm 
First of all you will need to install npm if you dont have it already.(npm is distributed with Node.js)

*  **Mac**
 <br/>`curl`
{% codeblock %}
curl "https://nodejs.org/dist/latest/node-${VERSION:-$(wget -qO- https://nodejs.org/dist/latest/ | sed -nE 's|.*>node-(.*)\.pkg</a>.*|\1|p')}.pkg" > "$HOME/Downloads/node-latest.pkg" && sudo installer -store -pkg "$HOME/Downloads/node-latest.pkg" -target "/"
{% endcodeblock %}
<br/>`Homebrew`
{% codeblock %}brew install node{% endcodeblock %}
<br/>`Mac pkg`
Just download and install package from [nodejs.org](https://nodejs.org/en/download/current)
<br/>

*  **Windows**
<br/>`Scoop`
{% codeblock %}scoop install nodejs{% endcodeblock %}
<br/>`Chocolatey`
{% codeblock %}choco install nodejs{% endcodeblock %}
<br/>`msi installer`
Download and install Windows Installer (.msi) from [nodejs.org](https://nodejs.org/en/download/current)
<br/>

## Install Hexo
After we got Node.js the installation of Hexo is straightforward:
{% codeblock %}npm install hexo-cli -g{% endcodeblock %}

## Setup
Now let's create our project with Hexo
{% codeblock %}hexo init PROJECT_NAME{% endcodeblock %}
This will create folder `PROJECT_NAME` and clone Hexo files from GitHub repository
to our local system.
next:
{% codeblock %}cd PROJECT_NAME & npm install{% endcodeblock %}
At this point all necessary files should be in place. To confirm
we can run:
{% codeblock %}hexo server{% endcodeblock %}
This will start Hexo local server and allow us to view our blog in web browser
under address mentioned in command output (`http://localhost:4000`).
And here it is
![Imgur](https://i.imgur.com/pZRwOa5.png)
<br/>

Ok! but this is only "Hello World" template so press CTRL+C and let's start configuration.
<br/>
* **Theme**
You can create own theme or if you like me and can't wait to start blogging use one from already existing. Lot's of themes can be found on Hexo [website](https://hexo.io/themes/). Installation of themes is very easy: 
	+ Pick theme 
	+ clone it to theme folder 
	+ edit _config.yml with {% codeblock %}theme: THEME_NAME{% endcodeblock %}

Themes have they own _config.yml in which we can edit menu items, title etc. I'm pretty sure anybody can hadle configuration and customization of theme.
<br/>
* **_config.yml**
For overal explenation please visit https://hexo.io/docs/configuration.html.
From my side i would sugest to install [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git).
{% codeblock %}npm install hexo-deployer-git --save{% endcodeblock %}
Then in main `_config.yml` add:
{% codeblock %}
deploy:
  type: git
  repository:
    github: <repository url>
{% endcodeblock %}
from now on every time when you will use {% codeblock %}hexo deploy{% endcodeblock %} it will push your website
straight to Github! Isn't that awesome ?
There is one more plugin that i highly recommend to install it's called [hexo-backup-git](https://github.com/coneycode/hexo-git-backup)
This will backup your whole hexo configuration so if you get into some trouble with some config or just want to get access to it from other computer
it is the best and quickest solution in my opinion.
Installation is easy as always:
{% codeblock %}npm install hexo-git-backup --save{% endcodeblock %}
as always add proper configuration in `_config.yml`
{% codeblock %}
backup:
    type: git
    repository:
       github: <repository url>
{% endcodeblock %}
usage of this plugin is same as in previous, to backup configuration type in terminal
{% codeblock %}hexo backup{% endcodeblock %} or {% codeblock %}hexo b{% endcodeblock %}

## Start blogging
After configuring Hexo, blogging is just a breeze:
* create new post md file
{% codeblock %}hexo new POST_FILE_NAME{% endcodeblock %}
* create new page md file
{% codeblock %}hexo new page PAGE_FILE{% endcodeblock %}
* Cleans the cache file (db.json) and generated files (public).
{% codeblock %}hexo clean{% endcodeblock %}
* start hexo server to see changes locally
{% codeblock %}hexo server or hexo s{% endcodeblock %}
* my favourite - website deploy
{% codeblock %}hexo deploy{% endcodeblock %}

Now you should be ready to start your blog journey on GitHub just like me. Good luck!
