- Working with Git CLI and Remote Repository
  
  **Introduction**
- Step 1 - Main Bash Run Command
  
  **Project Structure**
  
  * The main script, .bashrc, has to 'source' each sub-script files in order to load them.
  
  * We append the '.sh' extension to its filename, so that the editor Visual Studio Code ["VSC"] highlights the code.
  
  * The sub-script files should be located in a sub-folder 'src', e.g. '$HOME/src/gitrc'.
  
  * The filename of a sub-script should be the concatenation of a command and the suffix 'rc', e.g. 'gitrc'.
- Step 2 - Sub Bash Run Command
  
  **Sub-script File Structure**
  
  
  
  **Name Convention**
  
  * A vowel is prefered over a non-vowel for the middle letter of a 3-letter alias, e.g. 'dok' for docker.
  
  * Use three letters for the alias if the 2-letter alias is too similar to a keyword, bad word, etc.
  
  * The second letter can be from either a consonant, e.g. 'tf' for terraform, a sub-command, e.g. 'ga' for git add, or the last letter of the command, 'ce' for code.
  
  * The first letter should correspond to the run command.
  
  * Use two letters for the alias to represent a long run command, e.g. 'tf' for terraform.
  
  * Use one letter for the alias to represent a short run command, e.g. 'g' for git.
- Step 3 - New GitHub Repository
  
  **A New Repository from Scratch or Existing Folder**
  
  Create a folder to contain your project, assume it is named **myfolder/**
  
     $ cd myfolder
     $ git init
     $ git add <file>
     $ git commit <file> -m "<comment>"
  
  Optionally, create a **.gitignore** file to indicate all the files that you don't want to track.
  
  Before pushing the local files to your remote repository, go to your GitHub account and create a new <repo>, and SKIP the section on creating a README and / or LICENSE files.
  
     $ git remote add origin git@github.com:<user>/<repo>.git
     $ git push -u origin master
- Step 4 - Forked GitHub Repository
  
  **How to Clone a Repository into an Existing Folder**
  
  Assuming that you have an existing folder **myfolder/**
  
     $ git clone [http://github.com/dennislwm/myproject](http://github.com/dennislwm/myproject.git) temp
     $ mv temp/.git myfolder/.git
     $ rm -rf temp
     $ cd myfolder
     $ git reset --mixed origin/master
  
  If you would like to use secure SSH, you can replace the above **git clone** command with:
  
     $ git clone ssh://git@github.com/<user>/<repository name>.git temp
  
  The difference between **hard** and **mixed** is that **mixed** will keep local changes, while **hard** will discard those changes.
- Step 6 - Pushing Git
  
  **How to Add a Single File to Repository**
  
  First, you must add a file to a staging area, which is a temporary area before commit.
  
  (1) To add a single file:
  
     $ git add <file>
  
  (2) To add all files and folders
  
     $ git add .
  
  Next, you commit the file into the local repository.
  
  (3) To commit a single file:
  
     $ git commit <file> -m "description of changes to file"
  
  (4) To commit all files and folders in the staging area to a local repository:
  
     $ git commit
  
  Finally, you push all files and folders in the local repository to the remote repository:
  
     $ git push
- Step 7 - Undo Git
  
  **How to Remove a Single File from the Staging Area (Undo Git Add)**
  
     $ git reset HEAD -- <file>
- Step 8 - Pull Request
  
  (1) Go to your GitHub account and fork the repository that you want to make a contribution.
  
  (2) Clone the repository to your computer.
  
     $ git clone [https://github.com/enzoampil/fastquant](https://github.com/enzoampil/fastquant) fastquant
  
  (3) Navigate to the root folder of your local repository and create a branch, e.g. *remove-extra-columns*
  
     $ cd fastquant
     $ git checkout -b <your_branch_name>
  
  (4) Add and commit files just like in any regular repository.
  
     $ git add .
     $ git commit -m "Fixed bug issue #x"
  
  (5) Push changes to your branch, e.g. *remove-extra-columns*
  
     $ git push origin <your_branch_name>
  
  (6) Finally, go to your repository on GitHub, you'll see a "Compare & Pull request" buttion. Click on that button, and submit the pull request.
  
  **References**
  
  URL: [http://github.com/firstcontributions/first-contributions](http://github.com/dennislwm/myproject.git)
  
  ![][1]
  
  [1]: images/working-with-git-cli-and-github-repository/step-8---pull-request.png
- Step 9 - Test a PR Locally Before Merging
  
  (1) Run the following commands to Fetch the PR from GitHub where ID is the PR's ID
  
     $ git fetch origin pull/ID/head:<your-branch-name>
     $ git checkout <your-branch-name>
  
  At this point, you can do anything you want with this branch to determine if the PR is worth merging.
  
  (2) Add and commit any changes you want to the branch.
  
     $ git add .
     $ git commit -m "Modified PR"
  
  (3) Once satisfied return to master and merge your branch with the master.
  
     $ git checkout master
     $ git merge <your-branch-name>
  
  (4) Push to GitHub
  
     $ git push
  
  That's it, you have tested and closed the PR.
- Step 10 - Move directory from one repository to another with history
  
  Move one directory within a Git repository to root within another repository including its history, i.e. the goal is to move `myDir` into `dstRepo` with its history. 
  
  ---
- Repository Structure
     srcRepo/                         <-- Root of source repository
       +- myDir/                      <-- Directory to move
       +- otherDir/
  
     dstRepo/                         <-- Root of destination repository
       +- someDir/
	- 1. Prepare the source repository
	  
	  Clone `srcRepo` (make a copy but don't use your existing one) and delete `origin` repository to avoid accidentally making any remote changes.
	  
	  ```bash
	  $ git clone https://github.com/[USER]/srcRepo
	  $ cd srcRepo
	  $ git remote rm origin
	  ```
	  
	  Check that origin has been removed.
	  
	  ```bash
	  $ git remote -v
	  ```
	  
	  Using `filter-branch`, go through the complete history and remove all commits not related to `myDir`. From the git documentation:
	  
	  *Only look at the history which touches the given subdirectory. The result will contain that directory (and only that) as its project root.*
	  
	  ```bash
	  $ git filter-branch --subdirectory-filter myDir -- --all
	  ```
	  
	  *Note: you might need to add `--prune-empty` to avoid empty commits, in my case it was not necessary.*
	  
	  Check that `srcRepo` contains the contents of `myDir` in root.
	  
	  ```bash
	  $ ls
	  ```
	  
	  Perform some clean up.
	  
	  ```bash
	  $ git reset --hard
	  $ git gc --aggressive
	  $ git prune
	  $ git clean -df
	  ```
	  
	  Check that the result is correct.
	  
	  ```bash
	  $ git log
	  ```
	- 2. Merge into destination repository
	  
	  Clone `dstRepo` (make a copy but don't use your existing one) and create a remote connection to `srcRepo` as a branch in `dstRepo`.
	  
	  ```bash
	  $ git clone https://github.com/[USER]/dstRepo
	  $ cd dstRepo
	  $ git remote add <MY-BRANCH-NAME> ../srcRepo
	  ```
	  
	  Pull from the branch. Because your branch and master don't have a common base, `git` will refuse to merge them without the `--allow-unrelated-histories` option.
	  
	  ```bash
	  $ git pull --allow-unrelated-histories <MY-BRANCH-NAME> master
	  ```
	  
	  This will create a merge commit to merge the current HEAD with your branch. Type a commit message into the editor which should appear.
	  
	  ```
	  Move myDir from srcRepo to dstRepo into root folder.
	  ```
	  
	  After typing a meaningful commit message, proceed to push the changes.
	  
	  ```bash
	  $ git push
	  ```
	  
	  Clean up by deleting the cloned `srcRepo`, and if everything works, use `git rm` to remove `myDir` from `srcRepo`.
- Step 11 - How to Generate SSH Key for GitHub Authorization