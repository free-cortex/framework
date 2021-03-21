# Workflow 

### Create repository and essential files and branches
**00.** Create repository    
**01.** clone it locally   
```
git clone git@github.com:free-cortex/framework.git
cd framework
git init
```

**03.** Create a pdfs branch for the pdf files [(see more)](https://www.freecodecamp.org/forum/t/push-a-new-local-branch-to-a-remote-git-repository-and-track-it-too/13222).
```
git checkout -b pdfs
rm -rf * README.md .github .gitignore *swp ~.git .idea 
git add -A
git commit -m 'clean pdfs branch'
git push origin pdfs
git checkout main
```

**02.** create .gitignore  
Download and edit `.gitignore`
```
# Ignored paths and directories
## pycharm project path
.idea

## vim temporal files
**/*.swp

## temporal TeX files
**/*.aux
**/*.fdb_latexmk
**/*.fls
**/*.log
**/*.nav
**/slides/main.pdf
**/*.snm
**/*.synctex.gz
**/*.toc
**/*.vrb
**/slides/_minted-main/*.*
```


### Create Github Actions
**01** Create a deploy key with `ssh-keygen -m PEM -t rsa -b 4096 -C "your_email@example.com"` [:link:](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key)
[:link:](https://jdblischak.github.io/2014-09-18-chicago/novice/git/05-sshkeys.html)

**02.** Add a new secret variable called `DEPLOY_KEY` in 
https://github.com/free-cortex/framework/settings/secrets 

Where the value is taken from `id_rsa` with 
```
xclip -selection clipboard < ~/.ssh/id_rsa
```
which looks like:  
```
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
```
* Don't forget to "Adding your SSH key to the ssh-agent" and be authentified [:link:](https://docs.github.com/en/enterprise/2.13/user/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent)
```
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
ssh -T git@github.com 
```


### Create Github workflow
**03.** Create github action workflow
`.github/workflow/*.yml` and then create main.yml 
```
mkdir -p .github/workflows
cd .github/workflows && wget https://raw.githubusercontent.com/free-cortex/framework/main/.github/workflows/template.yml
mv template.yml $DESIRED_NAME.yml
```
* Setting up variables for pdf documents and keys in .github/workflows/main.yml. 
See this [main.yml](https://github.com/mxochicale/learning-latex-action/blob/master/.github/workflows/main.yml) as example.
Edit `main.yml` by changing branches and repository name.

**04.** Commit genesis in the main branch using `[skip ci]` in the commit 
```
git add . 
git commit -m "genesis commit [skip ci]"
git branch -M main
git remote add origin git@github.com:free-cortex/framework.git
git push -u origin main
```



### Create a new LaTeX doc

**00.** Raise an issue

**01.** checkout and pull main
```
git checkout main
git pull
```

**02.** create a new branch
```
git checkout -b $ISSUENUMBER-$BRANCHNAME
```

**03.** Create github action workflow
`.github/workflow/*.yml` and then create main.yml 
```
mkdir -p .github/workflows
cd .github/workflows && wget https://raw.githubusercontent.com/free-cortex/framework/main/.github/workflows/template.yml
mv template.yml $DESIRED_NAME.yml
```
* Setting up variables for pdf documents and keys in `.github/workflows/*.yml`. 
For instance, 
```
name: Compiling-TeX-$BRANCHNAME
on:
  push:
    branches:
      - main
      - 05-$BRANCHNAME
jobs:
  build:
    if: "!contains(github.event.head_commit.message, '[skip $BRANCHNAME]')"
```

See this [main.yml](https://github.com/mxochicale/learning-latex-action/blob/master/.github/workflows/main.yml) as example.


**04.** commit genesis and add path/files and push them
```
git add -A
git commit -m ':fire: genesis of $BRANCHNAME [skip branches]'
git push origin $ISSUENUMBER-$BRANCHNAME
```

* Edit README for new actions and readme icons
```
## Slides
[![GitHub Actions Status](https://github.com/free-cortex/framework/workflows/Compiling-TeX-Slides/badge.svg)](https://github.com/free-cortex/framework/actions) [![ieee-poster](https://img.shields.io/badge/read-slides-blue.svg)](https://github.com/free-cortex/framework/blob/pdfs/slides.pdf)
```

**05.** Create pull request
```
Title: [WIP] Drafting $BRANCHNAME
Content: Resolves #$ISSUENUMBER
If CI is successful, tex file will be built [here](https://github.com/free-cortex/framework/blob/pdfs/$PDFFILE.pdf)
```


**06.** keep macking commit until the issue is sorted out
```
git add -A
git commit -m ':fire: genesis of $BRANCHNAME [skip-all]'
git push origin $ISSUENUMBER-$BRANCHNAME
```
You can add [skip-all] to skip ci build or other flags added in workflow `*.yml`


### (b) Local build

#### Requirements 
* Install latest version of (i.e., Tex Live 2020 [:link:](https://github.com/mxochicale/latex/tree/master/installation)).
* sudo apt-get install python-pygments #https://tex.stackexchange.com/questions/40083/how-to-install-minted-in-ubuntu

#### local build
```
cd latex-doc/
make clean && make && evince main.pdf
make clean
```
