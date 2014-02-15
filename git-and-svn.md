git and svn
===

The big thing: GIT IS NOT SVN.

DO NOT THINK GIT IS JUST SVN WITH SOME ADDED BELLS AND WHISTLES.

These are two VERY different tools. At the end of the day, both provide versioning, but if you try to use git like you use svn, you will fail.

Accept now that you will have to re-think what you do. You will not be learning the equivalent git commands to do things that you do with svn because things are different.


### The new players

There are 21 total git sub-commands (compared to svn's 36 - see, it's easier). 

We'll only routinely use a handful of those, and here are the ones we'll use in the example below. I've added a comment to each command that mentions it's closest counterpart in `svn` (for example, `git add` and `svn add` do pretty much the same thing), but in many cases, they don't match up exactly (for example `git commit` and `svn commit` are VERY different).

1. add        
    - Add file contents to the index
    - similar to `svn add`
2. branch     
    - List, create, or delete branches
    - similar to `svn copy` but faster and simpler
3. checkout   - Checkout a branch or paths to the working tree
    - similar to `svn switch` but faster
4. clone      
    - Clone a repository into a new directory
    - like nothing you've ever seen before
    - when you `clone`, use the `ssh` protocol instead of `https` or `git`
5. commit     
    - Record changes to the repository
    - like `svn commit`, but local only
6. merge      
    - Join two or more development histories together
    - like `svn merge`, but simpler
    - by default, automatically commits (locally)
7. pull       
    - Fetch from and integrate with another repository or a local branch
    - like `svn up`, but more
    - just a shortcut for `git fetch` and `git merge`
8. push       
    - Update remote refs along with associated objects
    - similar `svn ci`, but not really

### Differences in a nutshell

#### git

- pros
  - faster (holy crap! this is not a joke)
  - workflow is more dynamic
  - local repo/commits
  - easy to commit code without breaking things
  - local commits of checkpoints
  - it's what the cool kids use
- cons
  - new tool
  - new terminology
  
#### svn

- pros
  - it's what we know
- cons
  - slower
  - sharing for reviews is harder


## New terminology

The big thing: GIT IS NOT SVN.

Here are some new things to learn.


### The meaning of `origin`

Unlike svn, with git you can have multiple remote repositories. 

I'm going to assume that we'll have only one remote repository for the foreseeable future.

Remote repositories are named, and the conventional name when you have a single remote repository is `origin`.

When you see a reference to a branch, it will typically be `branch_name` or `origin/branch_name`. If you see `branch_name`, it refers to a LOCAL branch; if you see `origin/branch_name`, it refers to a REMOTE branch (sometimes, you'll see `:branch` used, this also refers to a remote branch).


### Don't fear commitment

Unlike svn, `git commit` is a local operation. 

After you `commit` - you need to `push` that change to `origin` if you want to share it.

You can also `commit` many times between pushes.


### Switching between branches

Switching between branches is done via `checkout` and is almost as fast as changing directories (as in from `cd ../trunk` and `cd ../blah-1.3` with svn). 

As with svn, before switching, you need to `commit` your changes. Remember that in git `commit` is a LOCAL operation only. Committing changes locally has no impact on other users.


## New proposed workflow

It's very similar to our current approach, but there are two main branches:

 - instead of `trunk`, there is `develop`. 
 - the `master` branch (more below on it)

### The `master` branch

The `master` branch is made up of only stable releases.

Tags are used to identify releases.

Changes to `master` are fewer and larger, and are only merges.

To build a release, you always use `master` - either the tip or a tag.


### The release branches

Just like with svn, there are release branches, but they are merged in two directions: 

1. to `develop` (continuously) 
2. to `master` (when released).

Release branches are for bug fixes and stabilization (as they are now).

Release branches are named `release-x.y` and are created from either:

- `develop`: for a planned release
- `master`: for a hot fix of a bug in production

### The `develop` branch

This is analogous to our current `trunk`. 

### Examples

How do you do stuff?

#### Create a branch

    $ git checkout -b develop master

This creates a new branch named `develop` using the `HEAD` of the branch `master`.

#### Switch to a branch

    $ git checkout some_branch

This switches your current working copy to the branch `some_branch`.

### Where this idea came from

I used this as a starting point in spite of the scary picture at the beginning.

Don't read the top, scroll down to "the main branches" to start, then continue from there.
 
<http://nvie.com/posts/a-successful-git-branching-model/>

## New tools

- Sourcetree - I've been using this one, it's fast, free and shows what's happening pretty well
- git tower
- github desktop
- gitk
- gitx


## Existing tools

### IDEA

This seems pretty solid. I haven't tried any really interesting workflows yet, however.

### maven release plugin

This works, but has to be configured properly - there are two parts to this:

#### The SCM section

	<scm>
		<url>scm:git:https://github.com/lmeadors/pc-office.git</url>
		<connection>scm:git:git@github.com:lmeadors/pc-office.git</connection>
		<developerConnection>scm:git:git@github.com:lmeadors/pc-office.git</developerConnection>
		<tag>HEAD</tag>
	</scm>

#### The plugin section

	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-release-plugin</artifactId>
		<version>2.4.2</version>
		<configuration>
			<arguments>-DskipTests</arguments>
			<mavenExecutorId>forked-path</mavenExecutorId>
			<useReleaseProfile>false</useReleaseProfile>
		</configuration>
		<dependencies>
			<dependency>
				<groupId>org.apache.maven.scm</groupId>
				<artifactId>maven-scm-provider-gitexe</artifactId>
				<version>1.8.1</version>
			</dependency>
		</dependencies>
	</plugin>


### jenkins

//todo

## A complete example

This is how Brandon and I used it for `stripes-spring`.

Start with a clone:

    $ git clone https://github.com/bgoodin/stripes-spring.git

That is NOT a checkout. It is NOT the same as `svn co https://blah`. That CLONES the ENTIRE repository locally. It's not what's currently at the tip of the repository, it's the whole enchilada.

Since we didn't have a `develop` branch there, so we'll create one from `master`:

    $ git checkout -b develop master

Since we're working with an entire local copy of the repository, we need to push that branch back to `origin`:

    $ git push origin develop

Now that I have a develop branch, I can see the local branches by doing this:

    $ git branch
    * develop
      master

That means there are 2 branches - `develop` and `master`. 

The selected branch has an * in front of it - so `* develop` is my current branch.

Now I can chuck some code on that branch, but before I commit, I decide that what I'm doing is potentially risky, so I can create a local branch then, and commit the changes to that new branch:

    $ git checkout -b scary

> The Pro Git book (free from <http://git-scm.com/book>) calls this a "topic" branch: "A topic branch is a short- lived branch that you create and use for a single particular feature or related work."

That creates a new branch at the current starting point named `scary` and selects it.

    $ git branch 
      develop
      master
    * scary

Now I can continue hacking away on my changes without risking the `develop` branches stability.

Once I'm ready for Brandon to review it, I can commit and push that `scary` branch to `origin`:

    $ git commit -m "my crappy changes for review"

In svn land, that's it. I chucked the code and broke stuff and jenkins starts barking, and I get humiliated. Git makes me look smart! So I do this:

    $ git push

Next I tell Brandon to pull and review my code. He then replies with "It's not there. Did you push your branch?". 

I did push, but the `scary` branch that doesn't exist on `origin` yet. 

Git just made me look dumb. Nice. So now I do this:

    $ git push origin scary

Now, he does a `git pull origin scary` and gets my branch. 

He can hack it up and push it back to `origin`, then I can do a `git pull origin scary`, review his changes. 

Once we're both happy with the code, one of us can merge it to `develop` and `push` it back to `origin`.

I'll use the CLI to do that:

    $ git checkout develop
    $ git merge scary

Now, in my LOCAL repository, the two branches (`scary` and `develop`) are identical, but the REMOTE repository has `develop` back at the point that it was at when I branched. I need to `push` my repository back up to share that change:

    $ git push origin develop

Now, the 4 branches (`origin/scary`, `scary`, `origin/develop`, and `develop`) are identical. That means that the `scary` branch has served it's purpose, and is can be deleted.

    $ git branch -d scary
    $ git push origin :scary

That second command is non-intuitive. Here's how I think of it: Push *null* to the `scary` branch on `origin`.

To verify that the branches are as expected, you can ask git about ALL branches like this:

    $ git branch -a
    * develop
      remotes/origin/1.1.x
      remotes/origin/HEAD -> origin/develop
      remotes/origin/develop
      remotes/origin/master

Two things to note here - outside of the steps we went through for this example, we moved the `HEAD` reference to be `origin/develop`. When you clone a repository, you (by default) only get the `HEAD` branch.

### Setting up a new repository

This is easy:

    $ git init

Done.

Once you create a repository on github, they give you very clear instructions on how to push that new local repository to the remote one, but here they are again:

    $ git remote add origin https://github.com/you/some-repo.git
    $ git push -u origin master

After that, create a `develop` branch and push it to `origin`:

    $ git checkout -b develop master
    $ git push origin develop

Once that's done, you can set the default remote branch to `develop` by going to the repository page, then settings, then finally set the default branch there.

Now anyone else who clones your remote repository will get `develop` as the default branch after checkout.


## vi is better than emacs unless you're gay

<http://thkoch2001.github.io/whygitisbetter/>
