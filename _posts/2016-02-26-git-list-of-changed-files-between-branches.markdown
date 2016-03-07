---
layout: post
title:  "Git - Diffing All Changed Files Between Branches"
date:   2016-02-26 12:00:00 -0500
categories: git visual-studio
---

I've been pretty used to Git for years now, so I've gotten used to its standard workflow. I really enjoy seeing differences in files as a +/- system where you can compare the changes one after another.

At my latest company, we are heavily based in TFS, and are slowly moving to Git. It's a pretty good reminder that things that are obvious to me aren't necessarily obvious to anyone else, and I probably went through the same struggles myself, back when I first learned about Git.

A coworker is getting more used to collaborating in Git, and is more used to the "Visual Studio/TFS" workflow.

 His concern was that, when doing code reviews, he is used to comparing two commits, getting a list of all the files that are changed, and going through them one at a time to review. Seeing that list of changed files up front is useful to him, and doing everything from one environment instead of switching from Visual Studio to the command line.

Either Visual Studio or Beyond Compare are acceptable solutions, as long as it's not just 'compare this file, close the window, compare the next file'.

It just took a bit of searching, but I learned something new about Git and diffing branches that I'll probably use again.

Note that these findings were from the accepted answer <a href="http://stackoverflow.com/questions/29841994/git-diff-of-branches-or-commits-showing-file-list-overview-in-visual-difftool-an" target="_blank">here</a>.




First, I set BC to be my default diff tool and set the path for it:
{% highlight bash %}
git config --global diff.tool bc3
git config --global difftool.bc3.path "<path_to_bcomp>"
{% endhighlight %}

Then, I can tell git to do 'difftool' instead of the standard 'git diff'.
{% highlight bash %}
git difftool --dir-diff branch1 branch2
{% endhighlight %}

There are two things to note going on here.

If you do
{% highlight bash %}
git diff branch1 branch2
{% endhighlight %}
Then it will diff each hunk, one after another, file by file.

And the {% highlight bash %}--dir-diff{% endhighlight %} will still call out to Beyond Compare, but it will ask you file-by-file whether you'd like to diff it or move to the next one.

{% highlight bash %}
git diff branch1 branch2
{% endhighlight %}
Then it will diff each hunk, one after another, file by file.

which is the same as:

{% highlight bash %}
git difftool --d branch1 branch2
{% endhighlight %}

Once I do this, it will open a "folder compare" view in BC that shows all changed files in their respective files, and you can select them in any order and compare the files in each branch.

It's one of my missions to get people who are unused to Git not to be afraid of trying without throwing them into the deep end.