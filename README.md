Note: this is not an exhaustive tutorial, but should be enough to get you started. There is A LOT to learn with git, but for the most part, the contents of this tutorial should cover about 90% of the things you as a contributor will need to know.  

# Prerequisites
- Basic unix commands (ls, cd, mkdir, echo, cat) 

# Installing Git Locally:
1. MacOS: `brew install git` or `https://sourceforge.net/projects/git-osx-installer/`
2. Linux
    * debian based: `sudo apt install git`
    * Fedora based: `sude dnf install git`
    * Other: You don't need this tutorial, and should probably be the one teaching it.
3. Windows: `https://git-scm.com/download/win`


Notes:

1. By "locally" we mean on the end of your workspace. If you installed it in your desktop but tried to use it on a VM, it won't work.
2. Test if you have git already installed: `git --version`
3. How to install Github? --> you don't have to if you follow this tutorial.

# Version Control
Let's forget about Github for a bit and only focus on Git at the moment. The most important feature it has is the **version control**. 

This feature keeps track of the previous versions of your work, and can revert your project back to previous versions when something went wrong. To better understand this idea, let's take a look at the following **working tree** visualization. 

As shown here, each node in the working tree is a version, which is essentially a snapshot of all files when the node is created. In the git terminology, we call those nodes "**commits**" and the action of creating those nodes as "**commit changes**". Note the English word "commit" can be both a verb and a noun here.

![Image of Git working tree](./resources/Git.png)

Let's see how it actually works in the following experiment in my terminal.

### Init Git, Add and Commit changes
First, we create a directory and initialize Git in it. The ```git init``` command tells git to treat your current directory as a git repo(sitory). Git will create a hidden folder called ".git" in the current directory. Note this is an one-time-only command: once a git repo is established, you don't need to re-init it every time you commit changes.

```
$ mkdir git-intro
$ cd git-intro
$ git init . # use . to identify the current directory as a git repo
Initialized empty Git repository in /Users/jerric/workspace/workshop/git-intro/.git/
$ ls -a
.	..	.git
```

Then let's create a few dummy files to play our experiment on.

```
$ echo "file contents" > myfile.txt
$ echo "other file contents" > myotherfile.txt
$ ls -a
.	..	.git      myfile.txt		myotherfile.txt
```

Use ```git status``` to check what's been changed since last commit. Since we haven't committed any changes, the message indicate we have "No commits yet". It also prompted us that we have two "**untracked**" files. This is because we just created them and yet haven't commit this change.

```
$ git status

On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	myfile.txt
	myotherfile.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Now, let's try **track** these two files and commit this change. To commit a change, we have to go through two steps: ```git add``` + ```git commit```.

```git add``` is the command that adds files that you want to track into a "**staging area**". The staging area serves as an intermediate space before you actually commit the changes. You can add new files into, remove files from, and modify files in the staging area without making any actual commit. Once the files are put into the staging area, they are considered as "**tracked**" files.

```
$ git add myfile.txt
$ git add myotherfile.txt
$ git status

On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   myfile.txt
	new file:   myotherfile.txt
```

```git commit``` command creates a new commit in the working tree. As we introduced before, a commit is essentially a snapshot of every tracked files when the commit is created. More technically, what this command actually does is to take whatever you put into the staging area and record the changes made on them since the last commit. The files that haven't been put into the staging area (a.k.a untracked files) will be neglected, so their changes won't be recorded by this commit. Note we usually use ```-m``` flag to append some messages to describe what've been done in this commit.

```
$ git commit -m 'created two txt files'

[main (root-commit) cf34f6a] created two txt files
 2 files changed, 2 insertions(+)
 create mode 100644 myfile.txt
 create mode 100644 myotherfile.txt
```

Use ```git log``` to check the commit history. Where you may have to press enter to scroll to the bottom, and press `q` to quit. Above we see a complete list of all the changes that we have made, as well as the unique hash id of each commit. This hash id becomes important later. But essentially what we have here is a list of changes made, when they were made, and by whom they were made.

```
$ git log

commit a98898cda9cb78f18951a588cd288ae675e3cda0 (HEAD -> master)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Thu Jul 8 22:56:15 2021 -0600

    created two txt files
```


### Track changes and Recover from changes

Now, to demostrate how this version control can work, let's change our files a little bit and commit this change so as to create another version of the project

```
$ echo "adding other stuff" >> myfile.txt
$ echo “also here” >> myotherfile.txt
$ git add myfile.txt
$ git add myotherfile.txt
$ git commit -m 'made changes to the two files'
[main 414c43b] made changes to the two files
 2 files changed, 2 insertions(+)
```

Now, since we have two commits, let's experiment on the roll-back feature: Suppose you made some more changes to myfile.txt beyond our last commit and are not satisfied with our recent change, you can easily recover this single file with `git checkout` as follows

```
$ echo "adding other stuff for the second time" >> myfile.txt 

$ cat myfile.txt
file contents
adding other stuff
adding other stuff for the second time

$ git checkout myfile.txt

Updated 1 path from the index
```

Note that one file will now be returned to the most recent commit on your **working tree**. 

```
$ cat myfile.txt
file contents
adding other stuff
```

For larger mistakes, you may have to revert your whole local project to a previous commit with the following steps:

- Step 1: Find the hash id of a good commit from the output of `git log`
- Step 2: reset to this version

Where the output is telling you the commit message of the commit we have now returned to. This will have removed all local changes before hand, but will take you back to a place where hopefully things were functional.

How do we trace back to previous versions then? Suppose you are not satisfied with the recent change to myfile.txt and want to roll back to the previous version, all you need is to 

```bash
$ git log

commit 414c43b409702338f74dee46faac37e6467d3bcf (HEAD -> main)
Author: TeppieC <jerric.chen@cybera.ca>
Date:   Mon Jul 19 16:30:12 2021 -0600

    made changes to the two files

commit cf34f6a0f371b048527a6eec41dc441ac4510e7e
Author: TeppieC <jerric.chen@cybera.ca>
Date:   Mon Jul 19 16:16:52 2021 -0600

    created two txt files
    
$ git reset --hard cf34f6a0f371b048527a6eec41dc441ac4510e7e

HEAD is now at a98898c created two txt files

$ cat myfile.txt

file contents
```

Using git reset is good for changes on a local copy that you
definetly don't want to keep anymore. If you have pushed changes to a remote, however, git reset can cause difficulties for others. You can 
also use `git revert <sha>` to revert a particular commit or 
`git revert HEAD~N` to revert the last N commits. Revert also allows for 
removing individual "bad" commits, whereas reset deletes everything 
back to the desired commit.
You can try revert after recreating the changes outlined above by copying the commands below.
```
echo "adding other stuff" >> myfile.txt
echo “also here” >> myotherfile.txt
git commit -a -m "Changed both example files"
echo "adding other stuff for the second time" >> myfile.txt 
git commit -a -m "Update #2 on single file"
```

So the rule of thumb is "commit often" because when some parts of your work went wrong, you can always rely on this benefit of git to roll back your project to a previous commit and start over. 

Till now, we are solely working locally. In the next section, let's talk about working remotely with the help from Github.


# Collaboration
## Interacting with Github
Github is an extension of Git. It is a open-source community where you can share yours and access other's work, or use it as a tool to collaborate with others and manage the project. 

### Creating a repo
Github manages software projects in the way of **repo(es)**. To host your code on github and collaborate with your teammates, you'll need to create a **remote** repo that is at least accessible to your teammates. They are default to be public repos so that every one can see it. With github student pack, you can create private repos and share it only to your collaborators.

Now let's try to create a repo.

### Clone
The clone command lets you have a **local** copy of the remote repo on github. To clone the repo we just created, open a terminal and navigate to a directory you would like this repo to live, then type (note: you may be prompted to enter a username/password.):

```bash
$ git clone https://github.com/cybera/ECG-AI.git

Cloning into 'ECG-AI'...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (24/24), done.
remote: Total 27 (delta 6), reused 8 (delta 0), pack-reused 0
Receiving objects: 100% (27/27), 63.51 KiB | 613.00 KiB/s, done.
Resolving deltas: 100% (6/6), done.

$ cd ECG-AI
```

Then as a sanity check, let's type the following

```bash
$ git status

On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Note: Apart from the above method, github provides a few more ways to hook up the remote with local repos. For example, to push your local repository to github from a clean init you might
use the following commands to 1) add github tracking info, 2) rename the "master" branch to "main", 3) upload or "push" your repository to the url added as the remote in step 1. 

```
git remote add origin https://github.com/cybera/ECG-AI.git
git branch -M main
git push -u origin main
```



## Using branches
If you're making changes to someone elses repository, or if you're collaborating with someone, or even just trying something new, Git offers a great option called "branch(es)". 

![Demo of branch](./resources/Github.png)

To see all available branches. You will notice some of them are local branches and some are remote ones. 

```bash
$ git branch -a

* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
```

To create a new branch just for you

```bash
$ git branch MyNewBranch
```

and switch to this branch 

```bash
$ git checkout MyNewBranch

Switched to branch 'MyNewBranch'
```

this will copy all the code of the branch you're currently on, into a new branch where you don't have to worry about ruining someone's (or your own) hard work. This can be done with the following git command. You can return to your previous branch at any time with `git checkout [AnotherBranch]` 

Now that you're on your new branch, let's create a new file

```bash
echo "I created a branch" > myfileinbranch.txt
```

then let's see our git changes 

```bash
$ git status

On branch MyNewBranch
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	myfileinbranch.txt

nothing added to commit but untracked files present (use "git add" to track)
```

where we can now add these files to github and commit them :

```bash
$ git add myfileinbranch.txt
$ git commit -m 'commiting file to my branch' 
 [MyNewBranch eaea361] commiting file to my branch
 1 file changed, 1 insertion(+)
 create mode 100644 myfileinbranch.txt

```

Finally we push: 

```bash
$ git push --set-upstream origin MyNewBranch
```
The `--set-upstream origin MyNewBranch` is 
required only on the first push.



### Pull
The pull command fetches the newest updates (from your collaborator's changes) from the remote and merge that update to your local instance. Because no one has been working on this project since your last pull/clone, it prompts us with "already up to date". 

```
$ git pull

Already up to date.
```

### Push
Use ```git push``` to upload your local changes (a.k.a. commits) to the remote github repo. 

```
$ echo "I learned github" > afile.txt
$ git add afile.txt
$ git commit -m 'added afile'
$ git push

```
Now check your github repo and you will see this commit, so do your teammates.


### Pull request
Different branches serve different purposes. Conventionally, the **master/main** branch is where you publish releases. In smaller projects, this can also be a place where stable and working code stays at. If you are still experimenting your ideas, it's important to write on your own branch so that your changes won't affect others' work. When you are done, it's time to merge your code on your own branch to the master/main branch, we do it through a process called **pull request (PR)**. Pull request is essentially a process where your teammates can step in and review your work so that your mistake will be caught before it ruining the whole project. Though PR can be made using command line, I'm showing you how to do it through github as it's simplier and easier.

### Merge
If your work is not in conflict with that on the master/main branch, your branch **merge** to the master branch should work smoothly. That means you only need to click on the "merge" button to complete the process. However, sometimes your teammates are also working on the same file as you're working on it, and they merged to the master branch earlier than you, then you will have **conflict** and won't benefit from this "auto-merge" anymore. The proper way to solve this is to manually resolve the conflict. Let's see how to do that on github.

Merge not only happens in pull request. It can also happen when you are pulling new changes from remote to local or performing local branch merges. That means sometimes you'll have to manually resolve merge conflicts in your terminal. This will open up your vim editor and ask you to address the conflict in a similar way. 

If you do have a conflict that git cannot automatically merge you'll have 
something like the following in the affected files. It is up 
to you to replace the content between the angle braces
and create a version of the file that you want going forward.
```
other stuff
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
later stuff
```

## Preventing Yourself From Committing Things You Shouldn't

Suppose we have all our passwords stored in a file in our git directory. It would be a very bad idea to commit that. For example, suppose our directory looks like this: 

```bash
$ git status

On branch MyNewBranch
Your branch is up to date with 'origin/MyNewBranch'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        all_my_passwords.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Where the contents of `all_my_passwords.txt` is as follows:

```
netflix: SuperSecretSuperSecurePasswordIHopeNoOneGetsToReadThatWouldBeBad
```

We certainly don't want to commit that. But sometimes we need to have our passwords to access data/other resources. How do we make sure we don't accidentally send these to github?

With a "hidden" file called `.gitignore` , where we can list the files, directories, or filetypes that we don't want to commit to github. So let's create that 

```bash
$ echo "all_my_passwords.txt" >> .gitignore

# We can also ignore specific file types, for example, pdfs
$ echo "*.pdf" >> .gitignore 

$ cat .gitignore
all_my_passwords.txt
*.pdf
```

And now, if we type `git status` we will see that out passwords are still untracked. However, if we commit this to git 

```bash
$ git add .gitignore
$ git commit -m "adding files to ignore on git" 
$ git push
```

Now, if we type `git status`:

```bash
$ git status

On branch MyNewBranch
Your branch is up to date with 'origin/MyNewBranch'.

nothing to commit, working tree clean
```

We can no longer see our secret password file. As well, there are some protections build in to prevent us from accidentally commiting this file now

```bash
$ git add all_my_passwords.txt

The following paths are ignored by one of your .gitignore files:
all_my_passwords.txt
Use -f if you really want to add them.
```

# Github desktop
https://desktop.github.com/

# Additional resources
- A useful cheatsheet: http://rogerdudler.github.io/git-guide/
- The git documentation is always your best friend: https://git-scm.com/docs

