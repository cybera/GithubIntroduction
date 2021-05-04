Laura's test change

# Processes Covered In The Git Tutorial (With Expected Outputs)

Note: this is not an exhaustive tutorial, but should be enough to get you started. There is A LOT to learn with git, but for the most part, the contents of this tutorial should cover about 90% of the things you as a contributor will need to know.

# Installing Git Locally:
1. MacOS: `brew install git` or `https://sourceforge.net/projects/git-osx-installer/`
2. Linux
    * debian based: `sudo apt install git`
    * Fedora based: `sude dnf install git`
    * Other: You don't need this tutorial, and should probably be the one teaching it.
3. Windows: `https://git-scm.com/download/win`

# Cloning the Repository
To clone this repository, open a terminal and navigate to a directory you would like this tutorial to live, then type:

```bash
git clone https://github.com/cybera/GithubIntroduction.git
```
Where you may be prompted to enter a username/password. When that finishes, you can navigate to the new directory containing this project with
```bash
cd GithubIntroduction
```

Then as a sanity check, let's type the following
```bash
git status
On branch master
Your branch is up to date with 'origin/master'.

nothing added to commit but untracked files present (use "git add" to track)
```

# Creating Your Branch and Adding Files
To begin, if you're making changes to someone elses repository, or if you're collaborating with someone, or even just trying something new, it's a good idea to create a new branch just for you. This will copy all the code of the branch your on, into a new branch where you don't have to worry about ruining someone's (or your own) hard work. This can be done with the following git command

```bash
$ git checkout -b MyNewBranch
Switched to a new branch 'MyNewBranch'
```
You can return to your previous branch at any time with `git checkout AnotherBranch`

Now that you're on your new branch, let's create some files

```bash
echo "file contents" > myfile.txt
echo "other file contents" > myotherfile.text
```

then let's see our git changes

```bash
git status
On branch master
Your branch is up to date with 'MyNewBranch'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        myfile.txt
        myotherfile.text

nothing added to commit but untracked files present (use "git add" to track)
```
where we can now add these files to github and commit them :

```bash
git add myfile.txt
git commit -m “i am committing my first file to my branch”

[MyNewBranch 99e3f21] adding a file
 1 file changed, 1 insertion(+)
 create mode 100644 myotherfile.text
```
And the other
```bash
git add myotherfile.txt
git commit -m “a am adding the second file to my branch”
```
Finally we push:

```bash
git push

fatal: The current branch MyNewBranch has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin MyNewBranch
```

Where oh no! We have an error. This is because we forgot to tell git to start moving our changes to a new branch. We can fix that by doing what many before us have failed to do: Reading the error message and doing exactly what it tells us

```bash
 git push --set-upstream origin MyNewBranch

Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 558 bytes | 558.00 KiB/s, done.
Total 6 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
remote:
remote: Create a pull request for 'MyNewBranch' on GitHub by visiting:
remote:      https://github.com/cybera/GithubIntroduction/pull/new/NewBranch
remote:
To https://github.com/cybera/GithubIntroduction.git
 * [new branch]      NewBranch -> NewBranch
Branch 'MyNewBranch' set up to track remote branch 'MyNewBranch' from 'origin'.
```

Where we can now check if it worked

```bash
git status

On branch MyNewBranch
Your branch is up to date with 'origin/MyNewBranch'.

nothing added to commit but untracked files present (use "git add" to track)
```

# Preventing Yourself From Committing Things You Shouldn't

Suppose we have all our passwords stored in a file in our git directory. It would be a very bad idea to commit that. For example, suppose our directory looks like this:

```bash
git status

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

We certainly don't want to commit that. But sometimes we need to have our passwords to access data/other resources. How do we make sure we don't accidentally send these to gitlab?

With a "hidden" file called `.gitignore` , where we can list the files, directories, or filetypes that we don't want to commit to github. So let's create that

```bash
echo "all_my_passwords.txt" >> .gitignore
# We can also ignore specific file types, for example, pdfs
echo "*.pdf" >> .gitignore
cat .gitignore
all_my_passwords.txt
*.pdf
```

And now, if we type `git status` we will see that out passwords are still untracked. However, if we commit this to git

```bash
git add .gitignore
git commit -m "adding files to ignore on git"
git push
```

Now, if we type `git status`:

```bash
git status

On branch MyNewBranch
Your branch is up to date with 'origin/MyNewBranch'.

nothing to commit, working tree clean
```

We can no longer see our secret password file. As well, there are some protections build in to prevent us from accidentally commiting this file now

```bash
git add all_my_passwords.txt

The following paths are ignored by one of your .gitignore files:
all_my_passwords.txt
Use -f if you really want to add them.
```

# Tracking Changes

Let's change our files a little bit:

```bash
echo "adding other stuff" >> myfile.txt
echo “also here” >> myotherfile.txt
```
we can see these changes with `git diff filename`

```diff
git diff myfile.txt
diff --git a/myfile.txt b/myfile.txt
index f81fce0..980b369 100644
--- a/myfile.txt
+++ b/myfile.txt
@@ -1 +1,2 @@
 this is a file
+adding other stuff
```

Where there's a lot going on in this file. However, for the most part what we're most often interested in is the lines starting at `@@ -1 +1, 2 @@`. This line describes where the changes take place between our local file, and the file we've committed to github. Here the `-1` indicates the change has taken place in the first line, and the `+1, 2` indicates that we have gained two additional lines at the first line.
# Tracking Changes and Recovering When Things Go Wrong

## Change Tracking

To see how your files have progressed, you can use `git log` as follows

```bash git log

commit 38bb03af02f94b159b7f39c4bcc01bd01068b990 (HEAD -> MyNewBranch, origin/MyNewBranch)
Author: AlexIsBadWithComputers <alex.tennant@cybera.ca>
Date:   Mon Jan 25 16:25:25 2021 -0700

    adding files to ignore on git

commit 2d2e79a74edeccfb90ee5a22b315705cecc6d1e7
Author: AlexIsBadWithComputers <alex.tennant@cybera.ca>
Date:   Mon Jan 25 16:18:55 2021 -0700

    other message

commit 99e3f2152e66d222c9fb3ec7db3179f459cd569a
Author: AlexIsBadWithComputers <alex.tennant@cybera.ca>
Date:   Mon Jan 25 16:17:59 2021 -0700

    adding a file

commit 8e1289aac608ed906f81ae2b09822046c29fc242 (origin/master, origin/HEAD)
Author: Alex <alex.tennant@cybera.ca>
Date:   Mon Jan 25 13:46:34 2021 -0600

    Initial commit
(END)
```

Where you may have to press enter to scroll to the bottom, and press `q` to quit. Above we see a complete list of all the changes that we have made, as well as the unique hash id of each commit. This hash id becomes important later. But essentially what we have here is a list of changes made, when they were made, and by whom they were made

## File Recovery

### Single File
**Caution:** back up your mistakes before doing any of the following as un-committed local changes will be **lost forever**

If you've modified a file and have broken it beyond repair from the last git commit, you can easily recover this single file with `git checkout` as follows

```bash
git checkout filename

Updated 1 path from the index
```

Where that one file will now be returned to the most recent commit on github. For larger mistakes, you may have to revert your whole local copy with the following:

Step 1: Find the hash id of a good commit from the output of `git log`

Step 2: reset your local instance

```bash
git reset --hard 2d2e79a74edeccfb90ee5a22b315705cecc6d1e7

HEAD is now at 2d2e79a other message

```

Where the output is telling you the commit message of the commit we have now returned to. This will have removed all local changes before hand, but will take you back to a place where hopefully things were functional.

We then have to push this roll back back to github with

```bash
git push origin --force
```

Where we're saying to push our current state, our roll backed commit,  `origin` to the repo, and to force it ignoring that changes on git hub.
