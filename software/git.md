
## Git Tricks:
	If git ever says "you cannot push because there are other stuff on the remote" do a `git pull --rebase`
		> This will take all your local commits and 'replay' them on top of the remotes commits.
	If you accidentally did `git pull` do a `:cq` in the thingy (vim) and then
		`git merge --abort`
		`git pull --rebase`

	After performing a merge, you must:
		`git pull --rebase`

	Taking an SVN repository and keeping the history alive when converting to git:
		echo "timb = Tim Beaudet <timbeaudet@yahoo.com>" > users.txt
			> or any other users that are in both old/new systems.
		git svn clone -A "users.txt" path/to/svn/repo/ ./new_git_repo_folder
		
		cd into the new directory
		git remote add origin <repo ssh URL>
		git push -u origin master
		If you have issues, and the remote repo is clean / minimal (okay to be wiped to new state) use-
		git push -u origin master --force

	Tagging a release / build:
		`git push origin <name of tag> (or git push --tags)`
		Or use gitea releases tab.

	Moving a repository to point to a different remote:
		`git remote set-url origin url/to/new/remote/repo`
		`git remote -v`

	Changing/Fixing a broken commit-
		git log --oneline --graph
		stash everything in your working directory if there's anything (git stash)
		git rebase -i <commit ID>^      <commit ID> is from git log, -i = use interactive vim junk.
			or git rebase -i <commit ID of the commit before the one you want to edit>
			or git rebase -i @~2 or something
			In interactive editor replace "pick" with "e" for the commit you want to edit
				hit d, w      --to delete word
				i             --to go into insert
				e, space      --to add the e in front of commit
				ESCAPE        --to exit insert mode
				:wq           --write and quit
		git log --oneline     (verify the HEAD is where we want to play)
		
		NOW EDITING FROM THAT COMMIT, GO FIX THE BROKEN ISSUES

		git add --patch
		git commit --amend
		git rebase --continue
		git push --force    (ensure to check box in TortoiseGit)

	Add a remote to pull branches from a different repository:
		`git remote -v`                          Check if the remote is already existing.
		`git remote add REMOTE-NAME REMOTE-URL`  Add the remote
		`git fetch REMOTE-NAME`                  
		`git checkout BRANCH-NAME`               


	Process of adding a new feature:
		Doesn't actually matter if on master, but master must be up to date.
		`git checkout master`                    Make sure to get back to the master branch.
		`git pull --rebase`                      Make sure master is all up-to-date locally
		`git checkout other-branch`              Do this only if there is a reason to make a branch from different branch
		`git checkout -b feature/name`           Feature branch for the feature!!! feature/name-with-kababs

		Do code edits once finished...

		`git checkout master`                   Make sure master is all up-to-date, this and pull
		`git pull --rebase`                     
		`git checkout feature/name`             Make sure to checkout the feature branch where work was done.
		`git rebase master`                     Replays all feature commits on top of anything in master.
		`git checkout master`                   
		`git merge --no-ff feature/name`        feature/name can be omitted if you use `-` instead to represent "previous"
		`git push`

		To continue working on the branch, after merging to master, do
		`git checkout feature/name && git rebase master`

		Can even create the code edits first:
			Do code edits, once finished...
			
		Cleanup (delete) all the merged branches:
		DO NOT RUN UNLESS MASTER IS CHECKED OUT:
		`git branch | xargs git branch -d`
		IF deleted master, just checkout again.



		Running Git Bisect to find issue:
		Go to the root directory
		`git bisect start`
		`git bisect bad`
		`git bisect good <good commit id>`
			Create a build and test it out, if the issue works type
			`git bisect good`
			Otherwise is sucks so type
			`git bisect bad`
			Repeat until the broken commit emerges from the ashes.
			IF for some reason you can't build type in
			`git bisect skip`
			When finished with the search type in
			`git bisect reset`