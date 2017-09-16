# new-member-training: git merge conflicts

A merge conflict can come up when you're trying to merge two branches, but both edited the same file in the same place.  We'll walk through an example.

## Table of contents

#### [1. Setup](#setup)

#### [2. Your mission](#your-mission)

#### [3. Making a new branch](#making-a-new-branch)

#### [4. Committing our work](#committing-our-work)

#### [5. Simulating someone else's work](#simulating-someone-elses-work)

#### [6. Try to merge your branch into master](#try-to-merge-your-branch-into-master)

#### [7. Fixing the merge conflict](#what-the-merge-conflict-looks-like)

#### [8. Optional sections](#optional-sections)

##### [8a. Optional command line stuff](#optional-command-line-stuff)

##### [8b. Optional discussion about git workflow](#optional-discussion-about-git-workflow)


## Setup
Clone this GitHub repository.  This will download the files in this repository onto your local computer.
On the command line (i.e. Git shell):
```
git clone https://github.com/WHSDigitalMedia/new-member-training.git
```

If you want to learn some useful stuff for the command line, click [here](#optional-command-line-stuff) for some tips.


## Your mission

For training purposes we want to make a merge conflict happen.  We'll set up this situation:

![Merge conflict image](/img/merge_conflict.png "Merge conflict")

Take a look at the file `class_button.txt`; there are some things that need to be changed:
```
///Create Event
name = "";
action = noone;
text_color = c_white;
center = false;
font = fnt_comic_sans;
spr = spr_main_menu_start;
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

///Step Event
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

if isOver(self, 0, 0, 0, 0) {
    if mouse_check_button_pressed(mb_right) script_execute(action);
}

///Draw GUI Event
draw_self();
if sprite_get_width(spr) < strWidth image_xscale = 1.2 * (strWidth / sprite_get_width(spr));
draw_set_font(fnt_textbox);

if center {
    draw_set_halign(fa_center);
    draw_set_valign(fa_middle);
}

draw_set_color(text_color);
draw_text_transformed(x, y, name, global.textScale, global.textScale, 0);

draw_set_halign(fa_left);
draw_set_valign(fa_top);
```

Not only should the font be `fnt_textbox` instead of `fnt_comic_sans`, but we should be checking for a left mouse click, not a right mouse click!
We'll fix these changes in our own branch called `script-fixes`.


## Making a new branch
To make a new branch called `script-fixes` with PowerShell, type:
`git branch script-fixes`
Then you can check on the status of your branches by typing: `git branch`.

Edit line 6 in `class_button.txt` as such:
```
font = fnt_textbox;
```
Edit line 16 in `class_button.txt` as such:
```
    if mouse_check_button_pressed(mb_right) script_execute(action);
```

What do we do next?  We can check the status of our git repository:
```
git status
On branch text-fixes
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   class_button.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

It shows that `class_button.txt` needs to be staged.

If you want to brush up on your git, click [here](#optional-discussion-about-git-workflow) for an optional discussion about git workflow.


## Committing our work

Then let's stage and commit this to get ready to put it back onto the remote repository on GitHub.

To stage it:
```
git add class_button.txt
```
To commit it:
```
git commit -m "Change font and toggle action"
[script-fixes 6941ee1] Change font and toggle action
 1 file changed, 2 insertions(+), 2 deletions(-)
```
Note: `git add *` will add all changed files to the staging area. Generally speaking, `*` means 'all'.

Now we just have to go back to the master branch and merge this `script-fixes` branch into it.

This is how our repository looks like:

![Committed to text-fixes branch image](/img/merge_text-fixes.png "Committed to text-fixes branch")


## Simulating someone else's work

In the meantime while you were making that branch, Garrett with his super speedy typing fingers already updated `class_button.txt` with his favorite font.  However he was not a cooperative teammate and he already pushed this change to master.  Let's simulate this by editing the `class_button.txt` file on the master branch.

Switch to the master branch:
```
git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
```

Edit line 6 of `class_npc.txt` as follows (or at least have a favorite font that's not fnt_texbox):
```
font = fnt_impact;
```

Note that Garrett didn't pay attention to the mouse toggle and still left it at `mb_right`.

Stage and commit the file:
```
git add class_button.txt
git commit -m "Favorite font"
```

Now this is how our repository looks.  Thanks Garrett.

![Merge conflict image](/img/merge_conflict.png "Merge conflict")


## Try to merge your branch into master

Now is your time to shine with your git wizardry.

Make sure you're on the master branch:
```
git checkout master
```

Then to merge your changes (font is textbox, mouse toggle is left-click) into the master branch:
```
git merge script-fixes
Auto-merging class_button.txt
CONFLICT (content): Merge conflict in class_button.txt
Automatic merge failed; fix conflicts and then commit the result.
```

## What the merge conflict looks like
Here is the infamous merge conflict in all of its glory.  Open up `class_button.txt` and you'll see:
```
///Create Event
name = "";
action = noone;
text_color = c_white;
center = false;
<<<<<<< HEAD
font = fnt_impact;
=======
font = fnt_textbox;
>>>>>> script-fixes
spr = spr_main_menu_start;
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

///Step Event
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

if isOver(self, 0, 0, 0, 0) {
    <<<<<<< HEAD
    if mouse_check_button_pressed(mb_right) script_execute(action);
    =======
    if mouse_check_button_pressed(mb_left) script_execute(action);
    >>>>>>> script-fixes
}

///Draw GUI Event
draw_self();
if sprite_get_width(spr) < strWidth image_xscale = 1.2 * (strWidth / sprite_get_width(spr));
draw_set_font(fnt_textbox);

if center {
    draw_set_halign(fa_center);
    draw_set_valign(fa_middle);
}

draw_set_color(text_color);
draw_text_transformed(x, y, name, global.textScale, global.textScale, 0);

draw_set_halign(fa_left);
draw_set_valign(fa_top);
```

git tried to merge the files, saw that they were different in the same places, and is saying, "hey humans, please figure this out."
However it is nice and does show you both of the changes in the same file.

These parts are from the master branch (Garrett's edits)
```
<<<<<<< HEAD
font = fnt_impact;
=======
    <<<<<<< HEAD
    if mouse_check_button_pressed(mb_right) script_execute(action);
    =======
```

These parts are from the `script-fixes` branch (your edits)
```java
font = fnt_textbox;
>>>>>> script-fixes
    if mouse_check_button_pressed(mb_left) script_execute(action);
    >>>>>>> script-fixes
```

## Fixing the merge conflict

Talk with Garrett and reach an agreement on what the font and mouse toggle should be.
Then edit `class_button.txt` accordingly.  Sample follows:
```
///Create Event
name = "";
action = noone;
text_color = c_white;
center = false;
font = fnt_textbox;
spr = spr_main_menu_start;
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

///Step Event
strWidth = string_width(name) * global.textScale;
strHeight = string_height(name) * global.textScale;

if isOver(self, 0, 0, 0, 0) {
    if mouse_check_button_pressed(mb_left) script_execute(action);
}

///Draw GUI Event
draw_self();
if sprite_get_width(spr) < strWidth image_xscale = 1.2 * (strWidth / sprite_get_width(spr));
draw_set_font(fnt_textbox);

if center {
    draw_set_halign(fa_center);
    draw_set_valign(fa_middle);
}

draw_set_color(text_color);
draw_text_transformed(x, y, name, global.textScale, global.textScale, 0);

draw_set_halign(fa_left);
draw_set_valign(fa_top);
```

Checking the status of our repo:
```
git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   TrainingProgram.java

no changes added to commit (use "git add" and/or "git commit -a")
```

We want to push to the origin, but Git tells us that there are unmerged paths. We just fixed that error, so let's stage and commit our newly reconciled file.
```
git add class_button.txt
git commit -m "Reconciled font and mouse toggle"
[master 4bc3aba] Reconciled font and mouse toggle"
```

You can check the commit history with the command `git log` to see that all is well. To exit out of `git log`, type `q`.

The merge conflict is solved. Good job!

If you ever need to reference GitHub commands because you forgot them, here's a nice little [cheat sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf)! If you go there and still can't figure something out, check out [StackOverflow](https://stackoverflow.com/). 

## Optional sections 

### Optional command line stuff

To go into the folder containing the git repository, type the following command ("cd" == "change directory"):
```
cd new-member-training
```

To list all files in this folder, type: `ls`.

To print out the contents of a file (for example `class_button.txt`), type:
```
cat class_button.txt
```

If you want more info on these commands, look them up in the manual using `man`:
```
man cat
```

To exit out of the manual, press `q`.

Click [here](#your-mission) to get started on your mission.


### Optional discussion about git workflow

The files you see online on GitHub in a repository (i.e. at https://github.com/WHSDigitalMedia/new-member-training) are on the **remote repository**, also sometimes referred to as the **origin**.

When you clone the remote repository to your own local computer, you get a copy of the files there.  So the folder `new-member-training` that gets created that has all of the files in the remote repository is called the **local repository**.

The process of adding/removing/changing file(s) from your local repository to your remote repository goes:

1. **Add the changed file(s).**  This stages them, i.e. lets you select which files you want to add/remove.

2. **Commit the file(s).**  This neatly packages your changes with a commit message that lets everyone know why you made the changes, i.e. "Updated inventory sorting system."

3. **Push the commit.**  This uploads your changes to the remote repository.

You can [do this on command line](https://help.github.com/articles/managing-files-using-the-command-line/), or a client like [GitHub](https://desktop.github.com/) or [GitKraken](https://www.gitkraken.com/).

Let's say you want to add a file `class_player.txt` to your remote repository `training-programs`.  Here are the steps you'd take to do that:

0a. Find your remote repository `training-programs` on GitHub, and **clone it somewhere onto your computer**. To do so, first, press the green 'Clone or download' button. If you have the Github Desktop client, you can then press 'Open in Desktop'. Otherwise, copy the URL and type the following in commmand line. 
```git clone URL```.
    i. If there is no remote repository, you can [create one](https://github.com/new), assuming you have an account. 

0b. Let's say you cloned the repository to `~/GitHub/training-programs`. This is where your local repository is.  

0c. Now, create a file in that folder called `class_player.txt`.

Add, commit, and push the files following any of these (or your own favorite client):

- [the command line](https://help.github.com/articles/adding-a-file-to-a-repository-using-the-command-line/)

- [GitHub Desktop](https://help.github.com/desktop/guides/getting-started/)

- [GitKraken](https://support.gitkraken.com/getting-started/guide)
 
Now we're ready to commit our work!  Click [here](#committing-our-work) to go back.
