Git Training
============

Introduction
------------

1.  **What is git?**

    Git is a version control system (vcs).
    This means that git keeps track of how files are changed over time and allows you to easily restore any previous version of a file.
    You can also have multiple versions at a time, sync file across computers, and even edit them collaboratively with other people.
    Basically, git is a word processor's undo functionality on steroids.


1.  **Why should I use it?**

    You may already have an existing workflow using Word's revision system or Google docs for collaborative writing.
    Why, then, should you switch?

    1. *Die normative Kraft des Faktischen* ('the normative force of facts')  
       Git is used in a lot of our graduate level courses.
       If you have to deal with it anyways, you might just as well do it well.

    1. *Universality*  
       Word's revision system only works with Word files, and you have to have access to MS Word, which pretty much limits you to Windows and OS X.
       Google docs is available on every OS that has a modern browser, but again you are limited to a specific tool.
       In both cases you also have vendor lock-in: if Microsoft or Google stop supporting the product, you can kiss your version control system good-bye.
       
       Git has a very different approach: it is open-source and thus available for free on a large number of platforms.
       It has an enormous community, which ensures that it will be supported for decades to come.
       Git is not tied to a particular service infrastructure --- you can just use it locally without an internet connection, add an online service like Github or gitlab for distribution and backup purposes, or set up your own git server in a few minutes.
       Although it is designed with plain text files in mind, it works with all files (even binaries).
       Git works at the level of file systems rather than individual files so that you can keep track of the revision history of entire folders rather than just invidiual files.
       This is essential for any kind of project that involves more than one file, which includes writing research papers in LaTeX and all decently sized coding projects.
       Finally, git has excellent support for automatically merging multiple versions of a file into one that incorporates all changes, which is essential for collaborative writing.

1.  **Why should I not use it?**

    Git is very powerful, and with great power comes great responsibility.
    If you do not like tools that cannot be picked up intuitively in five minutes, and you are horrified by the thought that even after five years of extensive usage there will be certain git features you weren't aware of, look somewhere else.



Getting Started
---------------

### Software Setup

For this tutorial, we assume that you have the following tools installed: git, tig, and gitk.
If you have access to the virtual machine image of the Stony Brook Computational Linguistics Lab, all these tools are already set up for you.

There are many GUI tools for git, but we will mostly use the command line.
This has several advantages:

1.  Few GUI tools are available on all three major platforms (Windows, OS X, Linux).
    The command line version of git is available virtually everywhere (including BSD, Android, and iOS).

1.  Every GUI tool does things slightly differently.
    The command line is the same everywhere.

1.  Related to the previous point, the command line has a stable user interface.
    Sometimes features get added, but existing commands stay the same.
    GUIs, on the other hand, change with each iteration, moving things around and greatly altering the workflow in a futile attempt to maximize user-friendliness.

1.  GUI tools try to streamline working with git but actually end up muddling the core concepts behind git.

1.  If you know how to use the command line, you won't struggle with any GUI tool.
    The opposite does not hold.

1.  The command line tool is directly accessible from good text editors like vim and emacs.
    GUI tools are monolithic blocks that do not integrate with anything else.

1.  If you are in a text-only environment, for instance a remote server you are connected to via ssh, the command line is all you have.


### Initializing a Git Repository

Git's workflow revolves around repositories.
A repository is just a folder that also contains some hidden files and folders created by git.
So any arbitrary folder can be turned into a git repository.

Suppose you want to turn the folder `foo` in your home repository into a git repository.
This is very easy to do:

1.  Open a terminal.
1.  Enter `cd ~/foo` to change the current directory to `foo`.
1.  Run `git init`.

You now have a fully functional git repository.


### Cloning a Git Repository

One of git's main advantages is that it allows you to reuse repositories created by other people.
This is called *cloning*.
When you clone a repository, you create an identical copy of that repository on your computer, and you can work with that repository as if you had created it yourself.

Let us try to clone this very repository to your own computer so that you can add and delete files or edit existing ones.

1.  In the top-right corner of the website, you can see a green button that says *Clone or download*.
    Clicking on it reveals a URL.
    Copy this URL.

1.  Open a terminal and use the `cd` command to navigate to the folder that should contain the cloned repository.
    We will use the `mkdir` command to create a folder `training` in the home directory.
    Then `git clone` clones the repository into that folder. 
    You do not have to type the URL, just push `Shift + Insert`.

~~~bash
mkdir ~/training
cd ~/training
git clone https://github.com/CompLab-StonyBrook/git_training.git
~~~

1.  You now have a folder `git_training` inside your training folder.
    If you don't like that name, you can rename the folder.
    The repository will still work fine.

~~~bash
mv git_training my_git_playground
~~~


### Basic Git Workflow

You now have a working git repository on your computer. 
It does not matter whether you created it yourself from scratch with `git init` or cloned an existing one with `git clone`, how a repository is created does not affect its functionality.
For the rest of the tutorial I assume that you are working with a clone of this repository because this makes it easy to discuss specific examples.
Right now, the repository isn't all that interesting as it only contains this very tutorial and a pretty dull `LICENSE` file.
Let us do some actual work then.

First, it will be helpful for you to have a text file `notes` for taking notes while you work through the tutorial.
You can create this text file any way you want, the only thing that matters is that you save it in the repository.
In the virtual machine, you can use *nano* or *leafpad*.
Do not use a word processor like Word or LibreOffice, that only increases the likelihood that you accidentally create a doc-file.
We want a plain text file at this point.

You can also be extra-nerdy and create the text file directly on the command line:

~~~bash
cd ~/training/my_git_playground
echo '# My notes file' > notes
~~~

So now your folder contains a new file `notes`.
But even though the file is in your repository, it is currently not under git's version control.
You can verify this with git's status command:

~~~bash
git status
~~~

Git will give you quite a bit of information, most of which isn't relevant for us right now.
Find the line that says *Untracked files:*, and look at the list below.
You will see your `notes` file listed in read.
This means that git is aware that the file exists, but has not put it under version control because we did not tell it to.
In order to do so, we must use git's `add` command.

~~~bash
git add notes
~~~

If you run `git status` again, you will now see `notes` in green, under the heading *Changes to be committed*.

Why didn't git add the file automatically? 
Because a repository may contain files we do not want to put under version control.
For example, when you compile a LaTeX file the folder will be cluttered with files ending in `.aux`, `.log`, `.blg` and many others.
Since those files are created automatically from your `.tex` file, there is no reason to put them under version control.
But there's another reason, which brings us to the next important concept: *commits*.

Git has a three stage concept for keeping track of revisions.
A file starts out *unstaged*, which means that we currently do not record any changes to the file.
By using `git add` we tell git to *stage* a file.
Staging means that git records all changes that have been made to the file since the last entry in the revision history (if the file is completely new, the entire file is one big change).
The final step is the *commit*, which creates a new entry in the revision history that records the status of all staged files.
Commits are the backbone of git's revision history: a revision history is just a large graph whose nodes are commits, and each commit records the state of the files that were staged at this point.

In our running example, we have used `git add notes` to stage the notes file.
But we haven't commited it yet.
That's why `git status` lists `notes` under the heading *Changes to be comitted*.
Type `git commit`.
This will open a text editor where you are asked to enter a description of this commit.
Since we are only adding the first version of `notes`, we enter *initial commit* and close the text editor.
Alternatively, we could have also typed `git commit -m 'initial commit'` directly in the terminal.
Either way we now have created a commit.
Run `git status` again and git will tell you *nothing to commit, working directory clean*.
This means that you haven't made any changes since the most recent commit.

And this is all you need to get started with git.
Here's the basic workflow again:

1. Create and edit files as you see fit.
1. Run `git status` to get an overview of what has changed since the last commit.
1. Use `git add` to stage files.
1. Use `git commit` or `git commit -m <some text>` to commit the staged files.

*Tip*: If you want to stage all changed files in the repository, you can type `git add .` as a shorthand.
The `.` is a common convention for including all possible matches.

*Further reading:* In complex projects with multiple contributors, it will be important that you write good commit messages.
See [this blog post](https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message) for details.


### Remote Repositories

You now know how to use a repository on your computer, but git also allows you to sync repositories across computers.
To do this, you need access to a git server.
You can set up your own or use a service like *Github* or *gitlab*.
A repository that is stored on a server is called a *remote repository*.

If you cloned a repository from somewhere else, the location you cloned it from is already set up as a remote repository with the name *origin*.
You can then use the git command `pull` to get the newest version of the repository from the remote, and `push` to upload your latest commits to the repository.
The full syntax is `git pull/push <name_of_remote_repository> <branch>`.
Branches will be discussed later, for now you only need to know that by default every repository has a main branch, which is called *master*.
So if you aren't working with branches and are using the *origin* repository, the template above reduces to:

~~~bash
git pull origin master

git push origin master
~~~

If you try doing this with the training repository you cloned at the beginning of this tutorial, `git pull origin master` will work find but `git push origin master` will give you an error message.
That's because you only have read access to the remote repository, not write access --- it is not your repository after all, so why should you be allowed to change it?

In order to use `git push`, you need a repository with write-access.
We will create one on Github and add it as a remote repository.
On the Github page for the repository, you can see a button named *Fork*.
When you click it, Github will create a copy of the repository for your user account.
In that forked repository, click on the Clone button and copy the URL.
Make sure you are in your local repository in the terminal, then enter the following:

~~~bash
git remote add my_fork <URL to your repository>
~~~

Now you can push your changes to your fork with `git push my_fork master`.
And on any other computer you can get the most recent changes with `git pull my_fork master`.
The Github repository thus acts as a central sharing point for synchronizing your work across multiple computers.


### Github Only: Pull Requests

But what if you want to upload a change to the repository you do not have write access to?
For example, there might be several typos that you fixed, so it would make sense to incorporate those changes into the official repository, not just your fork.
For these situations, Github offers pull requests.
Check [Github's tutorial](https://help.github.com/articles/about-pull-requests/) for more information.



Intermediate Techniques
-----------------------

### Tags

Some commits are more important than others.
For instance, the last commit to your PhD thesis before you hand it in would mark the submitted version of the thesis.
That's not necessarily the final one, maybe you'll still fix some typos for the version you upload to your website.
But it is an important version that you'd like to easily identify among all your commits.
Whenever a commit represents a milestone, you can use a *tag* to clearly identify it.

Assigning a tag to the latest commit requires just one simple command:

~~~bash
git tag -a <tag_name>
~~~

A text editor will open where you can enter a detailed description for the tag.
Close the file and the tag will be created.
To see the list of available tags, just type `git tag`.
If you want to temporarily set back your repository to a specific tag, run `git checkout <tag_name>`.
To revert to the most recent commit, type `git checkout master`.
This way you can easily reset the repository to important previous stages to check something out and then return to the most current version.

Let's give this a try.
First, run `git tag -a clean` to create tag for the current version of the repository.
We will now make some easily visible changes to the repository:

~~~bash
echo `we are creating a new file` > new_file
git add .
git commit -m "playing with tags"
~~~

Now when you type `ls`, you will see all the files in the repository, which includes `new_file`.
But now try `git checkout clean` followed by `ls` --- `new_file` is gone!
That's because we have reverted the repository to the point before we created that file. 

*Tip*: You might wonder why the command is `git tag -a <tag_name>` rather than just `git tag <tag_name>`.
The latter is also a valid command, but the `-a` switch tells git to create an *annotated* tag so that you can enter a description. 
There's no real downsides to creating annotated tags, so you should use them by default unless you know enough about git to determine when a *lightweight tag* without annotation is a better choice.


### Throwing Away Modifications

While our little example for working with tags was instructive, it isn't something that should be part of the revision history.
Let's see what the revision history looks like right now.
Type `gitk`, which opens a GUI for browsing the history of the repository.
At the top of the history, you see our *playing with tags* commit as the most recent entry, with the *clean* tag preceding it.
Close the program and go back to the terminal, then type:

~~~bash
git reset --hard clean
~~~

Now start gitk again.
As you can see, the *playing with tags* commit is gone.
All the changes we have made since the *clean* tag have been completely wiped from the record.

Ideally, you won't have to use `git reset` very often, but sometimes you will want to experiment a bit and end up with crud in your history that should be excised.


### Branches

While adding and committing files is easy, it is a rather pointless exercise if you never do anything that uses the power of commits.
You might think that the main advantage is the ability to undo certain revisions and roll back everything to a previous state.
While that is indeed possible, git provides a tool that ideally will make such steps unnecessary: *branches*.

The idea behind branches is that a revision history shouldn't just be a linear string, but rather a graph.
Each branch in the graph represents a specific version of the repository that you can work on independently without affecting any of the other branches.
This sounds awfully abstract, but an example will make it clearer.

Suppose you are writing your first QP.
You have a git repository that contains all the files, and you have diligently documented the development of the QP with git commits.
At this point, your repository consists of a single branch, which is called *master*.
Every repository has such a master branch.
You aren't quite happy with the QP in its current form, but your advisor insists that you should send them the current draft so they can give you feedback.
Since there's no use in arguing, you send them the draft, but now what?
If you continue working on your draft, then it will be hard to incorporate the feedback --- your revisions will change page numbers, may change some phrasing, or completely drop some sections, so figuring out what the feedback was originally about will be tricky.
If you do not continue working on your draft, you lose valuable time.
You could make a copy of the whole repository and work on that copy while you wait for the feedback, which you can then incorporate into the original version.
But then you'll have a revised version and the copy you have been working on in the meantime, with no clear idea how the two could be recombined into one document.
That's a tough problem, but branches provide a simple solution:

1. Create a new branch *my revisions*.
1. Edit the files in this branch as you see fit.
1. Once you get the feedback from your advisor, edit the files in the master branch, which are still exactly the same as when you submitted the file.
1. Use git to merge the *my revisions* branch back into the master branch.


### Resolving Merge Conflicts


### Checking Out Commits Without Tags
