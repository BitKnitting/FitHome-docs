# Thanks to Those That Went Before
We get very excited when a person like Ben Keith has documented what he has done so that we may all benefit. Ben's post [_How To Use GitHub Wikis For Collaborative Documentation_](https://labs.inn.org/2014/05/19/applying-git-to-github-wikis/) showed us the way to collaborative GitHub Wiki collaboration.  THANK YOU BEN!  Even though the post was done almost six years ago, it is still very relevant.  
# Overview
As described in the [documentation contribution page](Contributing_to_documentation), contributors to the wiki edit files in the [FitHome-docs](https://github.com/BitKnitting/FitHome-docs) repo.
# Handling Pull Requests
Moderators have an additional remote fetch/push URL so that changes made in FitHome-docs can be pushed to the [FitHome wiki](https://github.com/BitKnitting/FitHome/wiki).

## Remotes
When you Fork the FitHome-docs repo, you set up the origin remotes.  Try the `git remote -v` command:
The remotes are then:
```
$ git remote -v
origin  https://github.com/BitKnitting/FitHome-docs.git (fetch)
origin  https://github.com/BitKnitting/FitHome-docs.git (push)  
```
You should see results similar to what we have above.

Moderators need to add the wiki remote:  
```  
git remote add wiki https://github.com/BitKnitting/FitHome.wiki.git  

```
Now `remote -v` should return:
```
origin  https://github.com/BitKnitting/FitHome-docs.git (fetch)
origin  https://github.com/BitKnitting/FitHome-docs.git (push)
wiki    https://github.com/BitKnitting/FitHome.wiki.git (fetch)
wiki    https://github.com/BitKnitting/FitHome.wiki.git (push)
```
## Commiting changes
The steps to updating the wiki to include changes include:  
- `git add .`
- `git commit -m <commit message>`
- `git pull origin master`
- `git push origin master`
- `git push -u wiki master`

