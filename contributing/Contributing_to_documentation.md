# Contact
<a href="mailto:contact@fithome.life">Email Us</a>  

# Overview of the FitHome Project
[Overview](Home)

# Contributing

We welcome contributions and suggestions to help us improve this project. THANK YOU.  Great documentation is critical to a project but sadly often lacking. 
# Thanks to Those That Went Before
We get very excited when a person like Ben Keith has documented what he has done so that we may all benefit. Ben's post [_How To Use GitHub Wikis For Collaborative Documentation_](https://labs.inn.org/2014/05/19/applying-git-to-github-wikis/) showed us the way to collaborative GitHub Wiki collaboration.  THANK YOU BEN!  Even though the post was done almost six years ago, it is still very relevant.  

__Let's get started!__  
  
# How Wikis work on GitHub
Lurking within every GitHub repo is a separate "special" wiki repo.  Our goal is to use a GitHub wiki for our FitHome documentation.  This requires a few unique operations in order to work.  The main thing is how to handle collaborative documentation.  
  
If you are new to using Git and GitHub, [the GitHub docs](https://help.github.com/) are quite useful.  

# Editing
All editing is done within the [FitHome-docs](https://github.com/BitKnitting/FitHome-docs) repo.  Follow the forking, cloning, and - after editing - the pull request steps provided by Jake Vanderplas' Youtube video [_Creating a Simple Github Pull Request_](https://www.youtube.com/watch?v=rgbCcBNZcdQ).
## Workflow
The overall workflow is:  
1.  [Fork the FitHome-docs repo](https://github.com/BitKnitting/FitHome-docs)
2.  Create a branch (git checkout -b my-branch)
3.  Stage and commit your changes (git commit -am 'description of my changes')
4.  Push the changes to your fork (git push origin my-branch)
5.  Submit a pull request.  
  
We'll get your pull request and then review your changes for publishing to the wiki.
## Working with Images
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
# Intra-Page Linking
You may want to link to a section within a page.  
There are two sides; 1) the page that holds the content 2) the page linking to the content

To link to the section on a wiki page, use the title with dashes around.

For example:  
  
```
## Mount Drive
```
might be a title on the RaspPi page.  The link to this section would be:  
  
```
[blah, blah](RaspPi/#mount-drive)  
```
Was
- e.g. Page with content:   
## [Mount Drive](#mount_drive)  
- e.g. Page linking to content:  
 [SSHFS](https://github.com/BitKnitting/FitHome/wiki/RaspPi#mount-drive)  


# Use Permanent GitHub Links
When linking to GitHub content, use permanent links as discussed on the [Getting permanent links to files](https://help.github.com/en/github/managing-files-in-a-repository/getting-permanent-links-to-files) post. For example, the latest version of readings.py is at ```https://github.com/BitKnitting/FitHome_EDA/blob/master/readings/readings.py```.

what we want to link to is a permanent link to the specific version at the time of this writing.  To do this, hit the ```y``` key.  For the reading.py page this gets the link ```https://github.com/BitKnitting/FitHome_EDA/blob/dafae9d8a2a2da41ff365fd76922e46dcfbd63ee/readings/readings.py```

## GitHub Page Link to a Line Number
First, get the permanent link as discussed above.  Then [as noted in this SO post](https://stackoverflow.com/questions/23821235/how-to-link-to-specific-line-number-on-github), _Click on the line number you want (like line 18), and the URL in your browser will get a #L18 tacked onto the end. You literally click on the 18 at the left side, not the line of code._





















 This requires us to follow the guidelines outlined in our [Wiki Collaboration documenation](GitHub_Wikis_For_Collaborative_Docs).

Please become familiar with wiki collaboration prior to creating or editing any documentation.


### Workflow:





And of course you can always email us: [contact@fithome.life](mailto:contact@fithome.life`).

