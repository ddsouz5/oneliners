Starting a new git repository
* <https://kbroman.org/github_tutorial/pages/init.html>

        New repo from scratch:
        Create a directory to contain the project.
        Go into the new directory.
        $ git init.
        Write some code.
        $ git add . # if adding all files
        $ git commit -m "created git"
        From exisiting project also add .gitignore with files to ignore
        $ git add .gitignore
        $ git commit -m "added .gitignore"
        
Using branches for pull request

* <https://uoftcoders.github.io/studyGroup/lessons/git/branches/lesson/>
* <https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork>
    
Git alias

    git config --global alias.lg 'log --graph --oneline --decorate --all'
    git lg
    
Git config

    git config --global core.editor "vim"
    git config --global user.email myemail@email.com
    
