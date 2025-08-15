https://github.com/manishsharma-7/nextwork-security-secretsmanager/

TO create a Virtual Environment for Python3
python3 -m venv venv

To start the Vertiual Environment
source venv/bin/activate

to installed required library 
pip3 install -r requirements.txt

to verify installed packages
pip3 list



to start the applicaiton 
python3 app.py





--- Important commands for git

git init
git remote add origin <your-forked-repo-url>
git remote set-url origin <your-forked-repo-url>
git add .
git diff --staged
git commit -m "Comment"
git push
git push -u origin main
git push -u origin master    <----- to push by default to master/origin
git log

to remove old version/not getting pushed

git rebase -i --root
git stash <---- if above command gives an error


git config --global user.name "Name"
git config --global user.email you@example

git config --global credential.helper store     <------after entering password