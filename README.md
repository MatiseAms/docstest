# Introduction

This is the full documentation for the Matise workstack. You will find information here to setup your device to be able to develop websites with the Matise stack.

to develop this gitbook:
- ```npm install```
- ```npm run dev```


markdown cheatsheet ([here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet))

Project setup by @SHoogland


codeship deploy script to gh-pages branch

```bash
git config remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
git fetch --unshallow || true
git config --global user.email "office+codeship@matise.nl"
git config --global user.name "matise bot"
npm run dist
git fetch
git checkout -b gh-pages origin/gh-pages
git pull origin gh-pages
cp -R _book/* .
git add .
git commit -a -m "Update docs"
git push origin gh-pages
```
