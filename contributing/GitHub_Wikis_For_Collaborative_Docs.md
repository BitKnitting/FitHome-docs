# Overview
Lurking within every GitHub repo is a separate "special" wiki repo.  Our goal is to use a GitHub wiki for our FitHome documentation.  This requires a few unique operations in order to work.  The main thing is how to handle collaborative documentation.

# Docs Repo
The way we address collaboration is to use a separate repo, FitHome-docs, for editing.  All changes will be made in FitHome-docs.


edit in a separate FitHome-docs repo.  When the project updates, we'll push updates from the docs repository to the wiki on the FitHome project.
## Remotes
The remotes are then:
```
$ git remote -v
origin  https://github.com/BitKnitting/F-docs.git (fetch)
origin  https://github.com/BitKnitting/F-docs.git (push)
wiki    https://github.com/BitKnitting/F.wiki.git (fetch)
wiki    https://github.com/BitKnitting/F.wiki.git (push)
``
## Images
Images are tricky because we've introduced a directory structure within a GitHub wiki that is used to being flat.  The path we need to use will most likely not show the image on the local edit.  Say for example, there is the image:
```
FitHome-docs/monitor/images/RaspPi_pinout.png  
```
That is to be embedded within the markdown page:  
```
FitHome-docs/monitor/ElectricityMonitor.md
```  
Given that both files are in the monitor directory, the first guess at the image path is:  
```
![RaspPi Pinout](images/RaspPi_pinout.png) 
```
and the image shows up on our local copy.  However, it was not showing up on the GitHub wiki page until we changed the image path to include the monitor directory.  I.e.:  
```
![RaspPi Pinout](monitor/images/RaspPi_pinout.png) 
```
