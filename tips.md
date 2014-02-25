Tips
===

### Copying a repository from one server to another one

This is surprisingly easy from an existing local repository:

	$ git fetch --all
	$ git remote add [new_remote_name] [your new server's url]
	$ git push --all [new_remote_name]

That's it.

