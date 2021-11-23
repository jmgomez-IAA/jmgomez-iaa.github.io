---
title: "Control de versiones con Git LFS"
date: 2021-11-19
categories:
  - blog
tags:
  - git
  - git-lfs
  - admin
---

Git LFS is an open-source project and is an extension to Git. The goal is to work more efficiently with large files and binary files into your repository.

    Large files will grow the history of your repository every time they are updated;
    Large files will make fetching and pulling slower;
    An update of a binary file will be seen by Git as a complete file change, other than e.g. for a plain text file, where only the differences to the file are stored. If you have frequent changes to binary files, then your Git repository will grow in size. After a certain amount of time, Git commands will become slower because of the growing size of your repository.

So, when you have large files in your repository and/or a lot of binaries, then it is advisable to use Git LFS. Git LFS uses pointers instead of the actual files when the files or file types are marked as LFS files. When a Git LFS file is pulled to your local repository, the file is sent through a filter which will replace the pointer with the actual file. The actual files are located on the remote server and the pulled actual files are located in a cache in your local repository. This means that your local repository will be limited in size, but the remote repository of course will contain all the actual files and differences.

# Descripcion

I will try to explain why and when Git LFS should be used and how to use it.

# Procedimiento

## Instalacion
The installation will be done on Ubuntu and we assume that Git is already installed. As said before, Git LFS is an extension to Git and therefore needs to be installed separately:
```shell
sudo apt install git-lfs
First create an empty new Git repository:
$ mkdir mygitlfsplanet 
$ cd mygitlfsplanet 
$ git init 
Initialized empty Git repository in /home/user/mygitlfsplanet/.git/
```

Navigate to your Git repository (where the .git directory is located) and execute the following command in order to activate Git LFS:
```shell
$ git lfs install
Updated git hooks.
Git LFS initialized.
```

First, take a look at your .gitconfig file in your home directory. The following section has been added:

```shell
cat $HOME/.git/.gitconfig
[filter "lfs"]
   clean = git-lfs clean -- %f
   smudge = git-lfs smudge -- %f
   process = git-lfs filter-process   
   required = true
```
Navigate to the directory mygitlfsplanet/.git/hooks. The following hooks have been added/updated and contain git-lfs commands which will be executed when the hook is triggered:

    post-checkout
    post-commit
    post-merge
    pre-push

Also a directory mygitlfsplanet/.git/lfs is added which is the local cache we have been talking about.

## Configuration

Now that we have installed Git LFS for our repository, it is time to configure which file types we want to associate with Git LFS. This information will be added to a .gitattributes file in your repository. It is advised to commit and push this file to your repository in order that every developer works with the same Git LFS configuration. The most easiest way to associate a file type with Git LFS is by means of the git lfs track command. Let’s associate all jpg files to Git LFS:

```shell
$ git lfs track "*.jpg"
Tracking "*.jpg"
```

The .gitattributes file is created and contains the following information:
```shell
$ cat .gitattributes
*.jpg filter=lfs diff=lfs merge=lfs -text
```

What if we have a directory largefiles in our repository with large xml files and we don’t want to associate all xml files to Git LFS but only the ones that reside in that particular directory? We can track the directory largefilesand only associate the xml files in that directory with Git LFS:
```shell
$ git lfs track "largefiles/*.xml"
Tracking "largefiles/*.xml"
```
The only thing left to do, is to commit the .gitattributes file to our local repository.

## Git LFS in Action

Now that we are ready with all the preparation work, it is time for some action. We are going to add a root.jpg, root.xml and root.txt file in the root of our repository. We also add a largefile.jpg, largefile.xml and largefile.txt in the directory largefiles. Commit those files and with the following command we can verify which files are being tracked as Git LFS files:

```shell
$ git lfs ls-files
0282cb373a * largefiles/largefile.jpg
fc3b142235 * largefiles/largefile.xml
72d5491269 * root.jpg
```

This result is exactly as we expected: all jpg files are tracked by Git LFS and only the xml file inside the largefiles directory is being tracked by Git LFS and not our root.xml file and the two txt files. When you look at the files on your file system, you won’t see any difference between files tracked by Git LFS or not. That is because the Git LFS filters replace the pointer files with the actual content. This way, the usage of Git LFS is transparent to you as a user.

Now push everything to the remote repository. When you click in GitHub on a Git LFS File, the file is being displayed normally but at the top of the file it is indicated that the file has been stored as a Git LFS file.

## Git LFS With an Existing Repository

Up till now, we have shown how to enable Git LFS when we start a new repository and we know which files we want to associate with Git LFS. But what if you want to enable Git LFS to your existing repository? You can do so the same way as we have done for a new repository. From that moment on, new files or updates to files will be tracked by Git LFS. The commits before you have enabled Git LFS, will not be automatically migrated. There is a way however to migrate your entire repository. You have to migrate all your existing branches by means of the following command:

```shell
$ git lfs migrate import --include="*.jpg,largefiles/*.xml" --include-ref=refs/heads/master
```

The above example shows the command which should be used if we had forgotten to associate any file types to our previous created repository. After the include option you specify which file types have to be migrated, after the include-ref option the branch you want to migrate. After this, your history will have been migrated to LFS. But be careful, this migratecommand will also rewrite your history! Your repository history will have different commit hashes and therefore every developer should clone the repository anew after this action. Think carefully of the consequences before you execute this migration.

# Tips

- The local Git LFS cache will not be cleaned up automatically. Just like you have to prune remote branches on a regular basis, you also have to prune your Git LFS content with the following command: git lfs prune

- Ensure that all the developers have Git LFS installed. When someone without Git LFS installed commits a file which should be associated with Git LFS, you will get some strange errors. They can be fixed, but it is better to prevent this from happening.

- We have also mentioned committing binaries to a Git repository, but is it advised to do so? As a first answer I would say no. But sometimes you just don’t have valid alternatives. Think about the following when you consider committing binaries:
      * Is it really necessary to put the binary under version control?
      *  Is there a text-based alternative for the binary? E.g. assume you want to commit MS Word files, is it possible to convert them to plain text or are there valid arguments not to do so?


# Conclusiones
In this post we explained what Git LFS is, how you can install it and how you can use it. We also explained how you can apply Git LFS to an existing repository and gave some tips.

# Referencias
[Why and How to Use Git LFS](https://dzone.com/articles/git-lfs-why-and-how-to-use)
[Learn Version Control with Git](https://www.git-tower.com/learn/git/ebook/en/command-line/advanced-topics/git-lfs)


 git lfs track "*.SchDoc"
 git lfs track "*.PcbDoc"
 git lfs track "*.pdf"
 git lfs track "Fabrication-Gerber/*"
  cat .gitattributes