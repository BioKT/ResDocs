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

Now modify the corresponding files of any section you want or create your new section or subsections by adding the corresponding files in the docs/ directory and editing the mkdocs.yml file to include them.

In case you already have a clone of the remote repo, you would like to update your local repo to work over the last contributions made by the rest of the contributors. This is done with the command:

```console
git remote add upstream https://github.com/BioKT/ResDocs.git
```

Once you are done you will have to add your contributions to the version control using:

```console
git add <your file or directory>
```

you can use the command `git status` to check the status of the files. Now that your new version is being tracked it is time to include your changes in the remote repo.

```console
git commit -m "Add brief explanation of editions or contributions"
git push
``` 

Once your commitments are pushed in the remote repository you can call MkDocs to re-built the web site including your contributions. To do so, run:

```console
mkdocs gh-deploy
```

It is a good idea to use `mkdocs serve` in order to see your contributions added to the web site locally before adding them officially to the online version.

Congratulations you are now a contributor of our ToolBox!! 
