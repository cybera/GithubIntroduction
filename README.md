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
2. How to install Github? --> you don't have to if you follow this tutorial.
3. Test if you have git already installed: `git --version`

# Version Control
Let's forget about Github for a bit and only focus on Git at the moment. The most important feature it has is the **version control**. 

This feature keeps track of the previous versions of your work, and can recover you from failure when something went wrong by reverting back to previous versions. To better understand this idea, let's take a look at this **working tree** visualization. 

![Image of Git working tree](https://github.com/cybera/GithubIntroduction/blob/jerric/resources/Git.png)

As shown in this visualization, each node in the working tree is a version, which is essentially a snapshot of all files when the node is created. In the git terminology, we call those nodes "**commits**" and the action of creating those nodes as "**commit changes**". Let's see how it actually works in the following experiment.


### Commit changes
First, we create a repo(sitory) and initializing Git in it. This tells git to treat your current directory as a git repo(sitory). Git will create a hidden folder called ".git" in the current directory. Note this ```git init``` command is one-time-only: once a git repo is established, you don't need to re-init it when committing any changes.

```
$ mkdir git-intro
$ cd git-intro
$ git init . # use . to identify the current directory as a git repo
```

Then let's create a few dummy files to play our experiment on.

```
$ echo "file contents" > myfile.txt
$ echo "other file contents" > myotherfile.txt
$ ls
myfile.txt		myotherfile.txt  
```

Now use ```git status``` to check what's changed since last commit. Since we haven't committed any changes, the message indicate we have "No commits yet". It also prompted us that we have two "**untracked**" files. This is because we just created them and yet haven't commit this change.

```
$ git status

On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	myfile.txt
	myotherfile.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Now, let's try **track** these two files and commit this change. 

```git add``` is the command that adds files that you want to track into a "**staging area**". The staging area serves as an intermediate space before you actually commit the changes. You can add new files into, remove files from, and modify files in the staging area without making any actual commit. Once the files are put into the staging area, they are considered as "**tracked**".

```git commit``` command creates a new commit in the working tree. As we introduced before, a commit is essentially a snapshot of every tracked files when the commit is created. What the command actually does is to take whatever you put into the staging area and record the changes made on them since the last commit. The files that haven't been put into the staging area (a.k.a untracked files) will be neglected, so their changes won't be recorded by this commit. "Commit often!" because when some parts of your work went wrong, you roll back your project to a previous commit and start over.   


```
$ git add myfile.txt
$ git add myotherfile.txt
$ git commit -m 'created two txt files'

[master (root-commit) a98898c] created two txt files
1 file changed, 1 insertion(+)
 create mode 100644 myfile.txt
```

Use ```git log``` to check the commit history. Where you may have to press enter to scroll to the bottom, and press `q` to quit. Above we see a complete list of all the changes that we have made, as well as the unique hash id of each commit. This hash id becomes important later. But essentially what we have here is a list of changes made, when they were made, and by whom they were made.

```
$ git log

commit a98898cda9cb78f18951a588cd288ae675e3cda0 (HEAD -> master)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Thu Jul 8 22:56:15 2021 -0600

    created two txt files
```


### Track changes
Let's change our files a little bit:

```
echo "adding other stuff" >> myfile.txt
echo “also here” >> myotherfile.txt
```
we can see these changes with `git diff filename`

```
$ git diff myfile.txt

diff --git a/myfile.txt b/myfile.txt
index d03e242..b451dd0 100644
--- a/myfile.txt
+++ b/myfile.txt
@@ -1 +1,2 @@
 file contents
+adding other stuff
```

Where there's a lot going on in this file. However, for the most part what we're most often interested in is the lines starting at `@@ -1 +1, 2 @@`. This line describes where the changes take place between our local file, and the file we've committed to github. Here the `-1` indicates the change has taken place in the first line, and the `+1, 2` indicates that we have gained two additional lines at the first line. Now let's try to commit this change.

### Recover from changes
If you've modified a file and have broken it beyond repair from the last git commit, don't worry, you can easily recover this single file with `git checkout` as follows

```
$ git checkout myfile.txt

Updated 1 path from the index
```

Where that one file will now be returned to the most recent commit on your **working tree**. For larger mistakes, you may have to revert your whole local copy to a previous commit with the following:

- Step 1: Find the hash id of a good commit from the output of `git log`
- Step 2: reset to this version

Where the output is telling you the commit message of the commit we have now returned to. This will have removed all local changes before hand, but will take you back to a place where hopefully things were functional.

How do we trace back to previous versions then? Suppose you are not satisfied with the recent change to myfile.txt and want to roll back to the previous version, all you need is to 

```
$ git log

commit 9ad6f2e7c001ab31aebd3b7599dcd90396486b7b (HEAD -> master)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Thu Jul 8 23:25:28 2021 -0600

    made changes to the two files

commit a98898cda9cb78f18951a588cd288ae675e3cda0
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Thu Jul 8 22:56:15 2021 -0600

    created two txt files
    
$ git reset --hard a98898cda9cb78f18951a588cd288ae675e3cda0

HEAD is now at a98898c created two txt files

$ cat myfile.txt

file contents
```

Till now, we are solely working locally. In the next section, let's talk about working remotely with the help from Github.


# Collaboration
## Interacting with Github
Github is an extension of Git. It is a open-source community where you can share yours and access other's work, or use it as a tool to collaborate with others and manage the project. Create your account here if you haven't done so.
ssh key?

### Creating a repo
Github manages software projects in the way of **repo(es)**. To host your code on github and collaborate with your teammates, you'll need to create a **remote** repo(sitory) that is accessible to your teammates. They are default to public repos so that every one can see it. With github student pack, you can create private repos and share it only to your collaborators.

### Clone
The clone command lets you have a **local** copy of the remote repo on github. To clone the repo we just created, open a terminal and navigate to a directory you would like this repo to live, then type (note: you may be prompted to enter a username/password.):

```bash
$ git clone https://github.com/TeppieC/github-intro.git

Cloning into 'github-intro'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.

$ cd github-intro
```

Then as a sanity check, let's type the following

```bash
$ git status

On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

Note: Apart from the above method, github provides a few more ways to hook up the remote with local repos. For example, you can also push your local directory to github.

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

Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 311 bytes | 311.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/TeppieC/github-intro.git
   9abb950..8397048  main -> main

```
Now check your github repo and you will see this commit, so do your teammates.

## Using branches
If you're making changes to someone elses repository, or if you're collaborating with someone, or even just trying something new, Git offers a great option called "branch(es)". 

![Demo of branch](https://github.com/cybera/GithubIntroduction/blob/jerric/resources/Github.png)

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

this will copy all the code of the branch you're currently on, into a new branch where you don't have to worry about ruining someone's (or your own) hard work. This can be done with the following git command. You can return to your previous branch at any time with `git checkout AnotherBranch` 

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
 
$ ls
README.md	afile.txt myfileinbranch.txt

$ git log
commit eaea3618370114d5e8243d1c4b355afe5edf0ad7 (HEAD -> MyNewBranch, origin/MyNewBranch)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Fri Jul 9 00:43:16 2021 -0600

    commiting file to my branch

commit 839704864b89f519c25bf698e843c4511dd8004c (origin/main, origin/HEAD, main)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Fri Jul 9 00:36:40 2021 -0600

    added afile.txt

commit 9abb950249422e54e3931b992cb68e5c9ae85b6d
Author: TeppieC <TeppieC@users.noreply.github.com>
Date:   Fri Jul 9 00:29:37 2021 -0600

    Initial commit
```

Finally we push: 

```bash 
$ git push

fatal: The current branch MyNewBranch has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin MyNewBranch
```

Where oh no! We have an error. This is because we forgot to tell github that we have a new branch locally. We can fix that by doing what many before us have failed to do: Reading the error message and doing exactly what it tells us

```bash
$ git push --set-upstream origin MyNewBranch

Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 8 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 358 bytes | 358.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
remote: 
remote: Create a pull request for 'MyNewBranch' on GitHub by visiting:
remote:      https://github.com/TeppieC/github-intro/pull/new/MyNewBranch
remote: 
To https://github.com/TeppieC/github-intro.git
 * [new branch]      MyNewBranch -> MyNewBranch
Branch 'MyNewBranch' set up to track remote branch 'MyNewBranch' from 'origin'.
```

Where we can now check if it worked on Github.

Now, to better observe how branch works, let's try switch back to the main branch where we were initially at

``` 
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.

$ ls
README.md	afile.txt

$ git log 
commit 839704864b89f519c25bf698e843c4511dd8004c (HEAD -> main, origin/main, origin/HEAD)
Author: Jerric Chen <jerric@Jerrics-MacBook-Air.local>
Date:   Fri Jul 9 00:36:40 2021 -0600

    added afile.txt

commit 9abb950249422e54e3931b992cb68e5c9ae85b6d
Author: TeppieC <TeppieC@users.noreply.github.com>
Date:   Fri Jul 9 00:29:37 2021 -0600

    Initial commit

```

### Pull request
Different branches serve different purposes. Conventionally, the **master/main** branch is where you publish releases. In smaller projects, this can also be a place where stable and working code stays at. If you are still experimenting your ideas, it's important to write on your own branch so that your changes won't affect others' work. When you are done, it's time to merge your code on your own branch to the master/main branch, we do it through a process called **pull request (PR)**. Pull request is essentially a process where your teammates can step in and review your work so that your mistake will be caught before it ruining the whole project. Though PR can be made using command line, I'm showing you how to do it through github as it's simplier and easier.

### Merge
If your work is not in conflict with that on the master/main branch, your branch **merge** to the master branch should work smoothly. That means you only need to click on the "merge" button to complete the process. However, sometimes your teammates are also working on the same file as you're working on it, and they merged to the master branch earlier than you, then you will have **conflict** and won't benefit from this "auto-merge" anymore. The proper way to solve this is to manually resolve the conflict. Let's see how to do that on github.

Merge not only happens in pull request. It can also happen when you are pulling new changes from remote to local or performing local branch merges. That means sometimes you'll have to manually resolve merge conflicts in your terminal. This will open up your vim editor and ask you to address the conflict in a similar way. 


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
bank: 12345
socialSecurityNumber: 123678445
visa: 4520123412341234
LocationOfGold:  -48.876667, -123.393333
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

## Github desktop
https://desktop.github.com/

## Additional resources
- A useful cheatsheet: http://rogerdudler.github.io/git-guide/
- The git documentation is always your best friend: https://git-scm.com/docs

