# Day to day

## Starting from a clean slate

You have two branches: `develop` and `master`. The current production release is from `master`.

You have 3 tasks: 234, 345, and 456:

 - 234 is a simple 1-hour task
 - 345 is a moderate 1/2 day task
 - 456 is a 1-2 day task and will require some experimentation

So, you decide to do 234, since it's simple and will give you time to think about the others.

You do the work on the `develop` branch, add your changes, commit them, and push the changes to `origin`:

	$ git add [your changed files]
	$ git commit -m "234 - something simple"
	$ git push origin develop

If you are using IDEA, you won't have to use `add` because it will automatically add modified files for you. If there are really new files, it won't add those for you, and you'll have to do that in IDEA or from the CLI (just as with svn).

As you were working on it, you had an idea for 456, but since you know it's a lunker, you decide to create a branch for the task: `task-456`. 

	$ git checkout -b task-456

You work on it for a while, and decide that maybe you aren't on the right path, so you decide to work on 345 for a while and think more on 456. 

You `add` your changes to the `task-456` branch and `commit` them, but since you're not sold that they are really what you want, you do not `push` them.

	$ git add [your changed files]
	$ git commit -m "456 - work in progress"

Now, you go back to the `develop` branch, and start on task 345. After a couple of hours, you discover that it's trickier than you thought it was, and 456 is still bothering you, and you have a new idea for it.

You decide that you want to table 345 for a bit and try your new idea for 456.

Aw, snap, you're on the `develop` branch, and change 345 isn't done. No worries. You simply create a `task-345` branch, add and commit your files there, and then go back to `develop`.

> Note: You could also use `stash` here, but I'm trying to keep this simple...

	$ git checkout -b task-345
	$ git add [your changed files]
	$ git commit -m "345 - work in progress"
	$ git checkout develop

Now, you aren't sure that any of the changes you made for 456 are relevant now, so instead of using the existing `task-456` branch, you decide to create a new `task-456-plan-b` branch and make your changes there.

	$ git checkout -b task-456-plan-b

After an hour, you realize that the change you thought was going to be a monster is actually pretty easy with the new approach. Your original approach is now complete trash. You finish up task 456 in a couple of hours and it seems to be what you needed, so now you can commit it and merge it back to the `develop` branch.

	$ git add [your changed files]
	$ git commit -m "456 - i wish i had thought of this earlier"
	$ git checkout develop
	$ git merge task-456-plan-b

Now, your original `task-456` code is trash and can be removed.

	$ git branch -d task-456
	error: The branch 'task-456' is not fully merged.
	If you are sure you want to delete it, run 'git branch -D task-456'.

Hm, thanks git - yes, I DO really want to delete that without merging it.

	$ git branch -D task-456

And since I've merged `task-456-plan-b` to `develop`, I no longer want it cluttering my history, so I can delete it, too.

	$ git branch -d task-456-plan-b

Now, it's time to wrap up 345, so we switch back over there and do that:

	$ git checkout task-345

An hour later, that's done and we're ready to add our changes, commit, and merge it back over to `develop`.

	$ git add [your changed files]
	$ git commit -m "345 - finally"
	$ git checkout develop
	$ git merge task-345

> Don't panic! This merge asks for a comment - and in my case, started `vi` to let me type it in. What the blazes? The merge of our earlier 456 branch was a "fast-forward" merge - it wasn't really a merge because there were not multiple streams at that point. This one is NOT a fast-forward merge, so we're asked to provide a comment about it. 

OK, so now, our `task-345` branch is also obsolete. It was useful for switching context as we were working, but now, it's merged and useless. We can delete it.

	$ git branch -d task-345

Now, we're all done for the day and can push our `develop` branch to the `origin` (remote) `develop` branch.

	$ git push origin develop

