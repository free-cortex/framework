# Workflow 

### Create repository
**00.** Create repository    
**01.** clone it locally   
```
git clone git@github.com:o-cortex/framework.git
git init
```

**02.** create .gitignore  
vim .gitignore
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
**01.** Add `shh-rsa` key in https://github.com/settings/keys following [the documentation](https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account) in github.

**02.** Add a new secret variable called `DEPLOY_KEY` in 
https://github.com/o-cortex/framework/settings/secrets 


Where the value is taken from `id_rsa` with 
vim ~/.ssh/id_rsa   
which looks like:  
```
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
```

### Create Github workflow
**03.** Create github action workflow
`.github/workflow/*.yml` and then create main.yml 
```
mkdir -p .github/workflows
cd .github/workflows && wget https://raw.githubusercontent.com/mxochicale/learning-latex-action/master/.github/workflows/main.yml
```
* Setting up variables for pdf documents and keys in .github/workflows/main.yml. 
See this [main.yml](https://github.com/mxochicale/learning-latex-action/blob/master/.github/workflows/main.yml) as example.
Edit `main.yml` by changing branches and repository name.

**04.** Commit genesis in the main branch using `[skip ci]` in the commit 
```
git add . 
git commit -m "genesis commit [skip ci]"
git branch -M main
git remote add origin git@github.com:o-cortex/framework.git
git push -u origin main
```


**05.** Raise an issue

**06.** Create a generated-pdfs branch for the pdf files [(see more)](https://www.freecodecamp.org/forum/t/push-a-new-local-branch-to-a-remote-git-repository-and-track-it-too/13222).
```
git checkout -b generated-pdfs
rm -rf * README.md .github .gitignore *swp ~.git 
git add -A
git commit -m 'clean generated-pdfs branch'
git push origin generated-pdfs
```

**07.** Create branch for drafting document
```
git checkout master
git checkout -b 01-drafting-slides
```

**08.** add path for latex document(s) 

**09.** commit changes
```
git add -A
git commit -m 'genesis of slides'
git push origin 01-drafting-slides
```

**10.** Create pull request
```
Title: [WIP] Drafting slides
Content: Resolves #1 
If CI is successful, slides will be build [here](https://github.com/mxochicale/nmc3/blob/generated-pdfs/slides.pdf)
```

### (b) Local build

#### Requirements 
* Install latest version of (i.e., Tex Live 2020 [:link:](https://github.com/mxochicale/latex/tree/master/installation)).
* sudo apt-get install python-pygments #https://tex.stackexchange.com/questions/40083/how-to-install-minted-in-ubuntu

#### local build
make clean && make && evince main.pdf
