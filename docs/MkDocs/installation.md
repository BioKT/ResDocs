# Installing MkDocs and other software.

The files that are used by MkDocs to built this website are located in the BioKT/ResDocs repository on GitHub [BioKT](https://github.com/BioKT) (let's call it remote repository). Although public, it requires some permissions to contribute directly. Once this permissions are aqcuired you are ready to start.

In order to modify/create files, all the work will be done locally in a "clone" of the original remote repository. Changes made will be then "pushed" to the remote repo and used to re-built the website.

The softwares required for this work are Git, MkDocs and presumably GitHub CLI.

    - Git is the version control to update/modify/create the files.
    - MkDocs builts the website.
    - GitHub CLI allows you to connect your local repo with the remote one via your GitHub credentials (HTTPS protocol).

* * *

## Installing software.

### Git.
- **Windows:** Git should be installed already.
- **MacOS:** Check if Git is already available: `git --version`. If not, it will prompt you to install it.
- **Linux:** 
    - Ubuntu: `sudo apt install git-all` should work.

### MkDocs.
- **MacOS:** Simply run the following command: `pip install mkdocs`.

### GitHub CLI.
- **MacOS:** Via Conda with the command: `conda install gh --channel conda-forge`.
Once installed, in the command line enter `gh auth login` and follow the instructions.
- Select HTTPS as preferred protocol.
- Use GitHub credentials to authenticate to Git.

* * *

## Contribute process.
Now you are ready to start your contributions! First thing you want to do is create a directory in your computer to store the clone of the remote repository:

```console
mkdir BioKT_toolbox
cd BioKT_toolbox
```

Inside this directory clone the remote repository with:

```console
git clone https://github.com/BioKT/ResDocs.git
```
If this is your first time, the cloned repository will be up to date. However, whenever in the future you want to contribute, you will need to ensure that your local repository is updated with the last contributions. Otherwise some unexpected errors will occur when trying to push your contributions into the remote repo.

In order to update your local repo it would be enough to run the command below. **REMEMBER, YOU SHOULD RUN THIS COMMAND EVERY SINGLE TIME YOU START A NEW CONTRIBUTION**.

```console
git pull origin master
```

If this is your first time updating the local repo, you will probably get a message talking about how should Git behave when it has to reconcile changes from your local repo and the remote repo. Just copy the first line that it recommends, something like:

```console
git config pull.rebase false # merge (the default strategy)
```

Now you can modify the corresponding files of any section you want or create your new section or subsections. Modifying already existing files is easy. Just use "vi" or whatever text editor you like and then save the changes. If you want to know how the syntax works (which is Markdown btw), check [this section](../../Markdown/syntax.md).
If you are creating a new section or subsection, you should first modify the mkdocs.yml file to include it. Generally a section (**How to contribute?**) has several subsections (**MkDocs**,**Markdown**) and each of this subsections are pointing to its own file (<ownfile>.md) in their corresponding `/docs/`directory.

Once you are done you will have to add your contributions to the version control using:

```console
git add <your file or directory>
```

you can use the command `git status` to check the status of the files. Now that your new version is being tracked it is time to include your changes in the remote repo.

```console
git commit -m "Add brief explanation of editions or contributions"
git push
``` 

Once your commitments are pushed in the remote repository you can call MkDocs to re-built the web site including your contributions. **This command is executed in the same directory as mkdocs.yml**. To do so:

```console
mkdocs gh-deploy
```

It is always a good idea to use `mkdocs serve` in order to see your contributions added to the web site locally before adding them officially to the online version.

Congratulations you are now a contributor of our BioKT ToolBox!! :)

Summary of commands for contribution work-flow:

```
git pull origin master
git add <file>
git commit -m "Explanation"
git push
mkdocs serve
mkdics gh-deploy
```
