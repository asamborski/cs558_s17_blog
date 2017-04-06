# CS 558 Network Security 
## Blog Posts Spring 2017

To post security news, go to the `_posts` folder and create a pull request.

This can easily be done from the browser by going to the [_posts](_posts/) folder and clicking `Create new file` in the upper right.

### Instructions
1. Name your file `YYYY-MM-DD-title.md` for example `2017-02-15-pegasus.md`
2. Write your notes in github flavored markdown. See this [cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).
3. Include at the top of the file the following front matter:

	```
	---
	layout: post
	title: Pegasus Spyware
	author: Your Names
	---

	Security news goes here...
	```

4. When you're done write a commit message and click `Propose new file`
5. Click "Creat Pull Request", and the TAs will review and approve it.
6. Drop a star for your favorite TA 😎

See an example [here](https://raw.githubusercontent.com/asamborski/cs558_s17_blog/master/_posts/2017-04-04-welcome-to-new-blog.md)

### Including Images

Inline images can be easily included

Instructions:

1. Upload image to `img/` folder
2. Reference image in markdown 

```
![alt text]({{ site.url }}/img/name_of_img.jpg "Title")
```


### Preview files locally

If you want to preview your notes locally you'll need to install [jekyll](https://jekyllrb.com/).
Then, just run:

	jekyll serve
	
	
#### For Ubuntu:
You will also need to install ruby version >= 2.0.0 and any dependencies that come up.

Then, from the main directory of the repository, just run:

	 bundle exec jekyll serve
	
It will tell you which address to go to in your browser to preview the page (default is http://localhost:4000).
