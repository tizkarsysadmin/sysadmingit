

git init



git remote add origin https://github.com/tizkarsysadmin/sysadmingit.git

or 

git remote set-url origin https://github.com/tizkarsysadmin/sysadmingit.git




git remote -v



git add "app implementation"
git add itop
git add racktables
git add images
git add README.md


git add .


git commit -m "initial commit"



git branch -M main
git push -u origin main

git add .
git commit -m "your message here"
git push


#### restore from last delete:

git reset --hard HEAD~1






#### ignore 1 file from history that delete:


git filter-branch --force --index-filter "git rm --cached --ignore-unmatch 'app implemention/opendcim/dcim2304.tar.gz'" --prune-empty --tag-name-filter cat -- --all


git reflog expire --expire=now --all
git gc --prune=now --aggressive

git push origin --force --all








