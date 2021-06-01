- Speed up your open source workflow with Bash functions
  
  #docker #bash #opensource #github
  
  This article is also available on Medium.
- Introduction
  
  I'm a developer and I use git, docker-compose, terraform, ssh in to evaluate open source projects. Now it doesn't make sense to make a continuous integration / deployment for them as I may be evaluating the project one time. 
  
  My typical workflow is to fork / clone a repository --> build a docker image --> create a terraform module --> deploy the project to a cloud server --> ssh to the cloud instance to perform setup.
- The problem
  
  Given the above circumstances, I have to type many long and repetitive commands to build and deploy my docker files, and rebuilding and redeploying them, if my first attempt fails, or subsequent attempts fail, as my open source is based on sparse documents and some trial and error. 
  
  I would be using the command lines git, docker, docker-compose, terraform, ssh at least 100 times per workflow, with the length of each command being 10-60 characters. The history feature of Bash is not perfect either, pressing the up and down keys until you find the command you are looking for requires more focus than you might expect.
- The goal
  
  I would like to replace most of the commands I use by 2 to 3 character aliases. I chose short commands that are not system commands, for example ga, gc, gco, gi, gp, gs, dib, dil, dcl, dcu, dcub, dcd, tf, tfs, tfa, tfp, tfd, tfi.
  
  Now, I'm gonna be using some of these commands regularly so that is not a problem. However, there are some commands that I may only use occassionally, which I won't recall after a while. I chose to create a cheat sheet, that will display a list of aliases, i.e. ha, hb, hd, hg, hs, ht.
  
  Then, there are some commands that require multiple parameters or a conditional statement. I chose to use a bash function, that prompts you for inputs. I chose to name these functions using hypens and longer keywords to distinguish them from aliases. I chose git-clone, git-create, git-new, git-pr, dc-cli, di-build, tf-apply, tf-plan, tf-import, tf-destroy, bash_ip, bash_ssh, bash_scp, scp-dn, scp-up.
  
  I chose to create a separate file / module for each command line, for example gitrc.sh, dockerrc.sh, terraformrc.sh, sshrc.sh.
- The solution
  
  I'm going to define my main script / module functions in .bashrc. This purpose of the main script is to define where the files or modules are kept and to load each of them. I define some global variables for paths, config and some helper functions, for example rc-code, rc-config. I use rc-code or  rc-config to make changes to module or config respectively.
- Conclusion
  
  My open source workflow is now faster and also more productive. No more typing repetitive long commands.
  
  When I need to make changes and reload the main script, I use aliases rcc and rcs. 
  
  This is me making changes to my main bash script, then loading them when I'm finished.
  
  rcc
  
  rcs
  
  Keep in mind that you might not need the exact functions I did, or you might need a different command line. It would be best if you paid some attention to which commands you use most often in your workflow, and define functions within a module for those.
  
  I hope you have a nice week. Work smarter, play harder.