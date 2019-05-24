# Git

Git is a form a version control software that provides a user with the ability to track changes of files, annotate changes and create branches. Git enables multiple people to work on the same project and whilst it is distributed (e.g. a copy of project is held by every user), a remote copy is used to keep everybody's version synchronised.

{:toc}

## Prerequisites

To start using git you will need:

* Git
* Git-LFS
* GitKraken (optional)

For installation instructions, please see the [installation instructions](installation-instructions) page.

## Useful git glossary

There are a few keywords to know when using git:

* **Remote** --- Stored on a server/service that's external to your computer. It is customary to call the remote server "`origin`".
* **Local** --- Stored on your computer.
* **Repository** --- An area containing the project files.
* **Commit** --- Adding one or more file changes to the repository, creating a log of the change and adding the changes to the repository's history.
* **Log** --- A history of a repository's commits is stored within git's log.
* **Push** --- Upload changes made in the local repository to the remote repository.
* **Pull** --- Download changes made on the remote repository into the local repository.
* **Branch** --- A unique version of a repository with its own history. A repository will normally have two branches: `master` and `develop`. Typically a new branch is created from an existing branch, thus inheriting the branch's history.
* **Fork** --- Create a new remote repository from an existing remote repository, whilst maintaining links to the original repository.
* **Tag** --- A labelled pointer that is attached to a point of a repository's history. This is typically used for recording version numbers.
* **Clone** --- Create a local copy of a remote repository.
* **Merge** --- The changes of one branch are copied into another.
* **Conflict** --- When the same file or part of a file has been changed in two branches, a merge cannot be performed without resolving the conflict due to the ambiguity of the correct resolution.   
* **HEAD** --- A pointer within a branch that points to the most recent change.
* **Rebase** --- Move a branch's pointer to a particular commit.
* **Stash** --- Temporarily move changes into a hidden area within the repository. This is useful when performing git commands that require all changes to be committed.
* **Pop** --- Move changes into from the repository's hidden storage area back into the repository (see _Stash_).
* **Submodule** --- The method git uses to enable a repository to reside within another repository.
* **Pull/merge request** --- Request a branch is merged into, e.g. `master`. This normally involves a code review process and is typically used on protected branches.
* **LFS** --- Git is ideal for handling changes to text files however tracking and storing large binary files can be problematic. Git's Large File Storage (LFS) support provides tools to handle storage such files. A repository will need to have LFS mode enabled to support pushing and pulling of LFS objects.

## Git usage

### How git works

Git monitors a directory and any child directories for changes. Git does this by making extensive use of `diff` --- a tool that finds the differences between files at a byte/character level. By using `diff`, git can tell when content has been added, removed or even moved. Git can also track when entire files have been moved or renamed. Git is therefore efficient in how it records changes over a repository's life --- essentially only changes to files are recorded.

Git records all changes within a `.git` directory located in the base of the repository. This maintains a record of all tracked branches

#### The need for LFS

Git works great for text-based file formats but finds it difficult to keep track of changes in binary files, particularly large binary objects, in a repository. Git LFS addresses this by creating special pointer files in lieu of the original file. The original file, meanwhile, remains seamlessly usable through applications and trackable with git.

The way Git LFS works is to make use of git's pre-commit and post-commit hooks. A combination of these hooks means that, on a commit, git will:

1. Hash the file and determine whether or not it has changed.
2. If the file has changed, it will copy the file into the repository as a whole (within `.git/`). Otherwise, the file is replaced with a text-based placeholder that contains a reference to the original file (with the same name as the original file).
3. The commit then takes place, committing the text-based placeholder with the reference to the original file.
4. Following the commit, the text-based placeholder is removed and the original file moved back into the original location.

When it is time to push the repository to a remote server, Git LFS is invoked and pushes the files to the remote as a whole (`git lfs push`). When using pulling from a remote server, all text-based placeholders with reference links are resolved using `git lfs pull`.

#### Configuring git

Git needs to be configured for a user account. This is typically only required when you use git for the first time and is used to set up how git tracks the author of changes.

#### Configuring a repository

Every git repository has its own set of configurations that can be adjusted. For example, it is possible to have multiple remote repositories and the default repository is set within the configuration.

In addition to configuring repository settings, git's behaviour can be changed by three files within the root folder of the repository:

* `.gitattributes` --- defines attributes for files including how a diff should be performed and what files should be tracked by LFS (see [git-scm docs](https://git-scm.com/docs/gitattributes))
* `.gitignore` --- containing a list of files or directories that should not be tracked by git (see [the section below](#configuring-git-to-ignore-files-and-directories)).
* `.gitmodules` --- defines a submodule (another git repository) within a git repository. This allows the importing of other git-managed projects within a repository (see [git-scm docs](https://git-scm.com/docs/gitmodules)).

#### Configuring Git to ignore files and directories

For any given repository, git can be told to not track particular file types, directories or a combination of the two. This can be accomplished by populating the `.gitignore` file within the base of the repository. Here are a few examples:

```gitattributes
# Ignore compiled library and executable files
*.lib
*.exe

# Ignore all log files within the build directory
# but not child directories
/build/*.log

# Ignore all tmp files within the build directory
# and all nested child directories. For example
# /build/1/2/example.tmp
/build/**/*.tmp
```

There may be an occasion where a wildcard is too overreaching, therefore making it desirable for an exception. Prefixing a line with `!` tells git to make an exception. For example:

```gitattributes
# Ignore everything within the output directory except the
# .gitkeep file
/output/
!/output/.gitkeep
```

Note that precedence is important: start with the blanket rule and then create an exclusion. The above example also demonstrates a popular method in adding an empty directory to a git repository using an empty `.gitkeep` file, as git will not add or track empty directories.

For more information on `.gitignore` files, check out [git-scm docs](https://git-scm.com/docs/gitignore.

### Commits and commit messages

Every time there is a change to the repository, it is necessary to document the change in a git commit message. A git commit message typically comprises a title of 72 characters and a multi-line description that should provide a more thorough description. Whilst there's no right or wrong on how a commit title or its message should be composed, git commit messages can be useful when looking for a particular change.

#### Commit titles

For this reason, it is recommended that the commit title describes what is being contributed to a repository. It is common practice to create a commit message that uses the imperative, present tense. i.e *Change* instead of *Changes* or *Changed*. The most important being **be consistent**. Some repositories will prefer that a commit title starts with one fo the following verbs:

* `Add` --- to denote an addition of a feature (e.g. `Add main menu`).
* `Change` --- to denote a change to a feature. (e.g. `Change menu colour to red`).
* `Remove` --- to denote a removal of a feature (e.g. `Remove password sync option`).
* `Fix` --- to denote a fix for a feature (e.g. `Fix buffer overflow on button click`).
* `Delete` --- an alternative to `remove`.
* `Update` --- to denote an update to a feature (e.g. `Update antimatter submodule to v1.3`).
* `Merge` --- to denote the merging of two branches (e.g. `Merge develop into master`).

If in doubt, check for the presence of a `CONTRIBUTORS` file within a repository as this should provide insight into how that repository is managed, including rules on how to commit.

#### Commit messages

Descriptive commit messages can also provide enough information to find a relevant commit in software such as [GitLab](https://cmseutc01.ddns.shef.ac.uk:4443/gitlab/) or when browsing through the git logs. Messages can span multiple lines if required. A good commit message describes the contents of the change and this may be bullet points (e.g. `- This is a bullet point in plain text`) or in paragraphs of plain text.

#### When to commit

Commits should be made often and kept small. This forces commits to be related and not spanning a number of unrelated areas. This allow enables faster distribution of changes to groups and thus reduces the possibility of merge conflicts.

Despite the recommendation being to commit often, commits should not consist of half-done work. If a clean repository is required in order to change branch or pull in a change, simply use git's `stash` feature.

Additionally (and ideally), a change should be tested before it is committed, thus reducing the number of unnecessary fixes that then make its way into the repository.

### Working with remote repositories

Whilst git will track changes of a repository for a single user, one of git's strengths is its ability to work with others, share code and merge changes. Git refers to a remote server that hosts the shared repository simply by the name `remote`.

#### Remote repositories

A repository can have one or more remote repository locations. Each remote can be tracked separately, although it's up to the user to manage each repository individually. By using a remote server, two functions become available: `push` and `pull`. Push will upload local changes to the remote repository, and pull will download remotely held changes to the local repository. These actions will also look for conflicts and merge changes wherever possible.

A connection to a remote repository is handled through one of two protocols: `https` or `ssh`. As the UTC uses a [custom-signed digital certificate](../security/#utc-digital-certificates), it is simpler to use the `ssh` protocol. Should the UTC's CA certificate be installed, then either protocol can be used.

#### GitLab

The UTC uses [GitLab](https://cmseutc01.ddns.shef.ac.uk:4447/) to manage projects and shared repositories. Check out the [UTC's GitLab documentation](./gitlab/) for more information on the GitLab service and learn how to use it, including tasks such as [*adding a repository*](./gitlab/#creating-a-new-gitlab-repository), [*cloning an existing repository*](./gitlab/#cloning-a-gitlab-repository), and [*pushing an existing repository to GitLab*](./gitlab/#pushing-an-existing-repository-to-gitlab).

### Using branches

Branches are an integral part of git and, unlike other version control systems, are fundamental to the development process. Git branches are effectively a pointer to one or more changes. A new branch from a snapshot allows the capturing and encapsulating new changes. Branches thus will typically have a common history (`master` or `develop`) before encapsulating independent changes.

Using branches can therefore make it harder for bad or unstable code or files to be merged into the repository's main `master` branch and enables cleaning of a repository's history before it is finally merged into the `master` branch.

Branches within git are extremely lightweight unlike other version control system models, allowing it to be used as often as required. Git management software will almost always allow easy creation of branches.

For more information on branches, check out [Atlassian's website on using branches](https://www.atlassian.com/git/tutorials/using-branches).

#### Git flow

Git flow is essentially a template that is used to manage branches within a repository. It is used predominantly in release-based software workflows where features, releases and hotfixes will be required. Git flow uses a two branch core of `master` and `develop` and then changes to a repository are categories within one of the following:

* **Feature (`feature/feature-branch-name`):** A feature branch introduces new functionality and should be created from a repository's `develop` branch.
* **Release (`release/release-branch-name`):** A release branch can be created after a number of features have been added to a `develop` branch and is used to direct effort towards a releasable version (and eventual merge into `master`). A release branch will not have new features added and only accepts bug fixes, documentation generation and release-oriented tasks. It should then be merged into `master` and tagged with a version number.
* **Hotfix (`hotfix/hotfix-branch-name`):** A hotfix branch is forked off of the `master` branch and is the only branch that should be forked from `master`. Following a hotfix, it should be merged into both `master` and `develop` branches.

Software such as GitKraken can help with git flow repository management, performing branches and eventual merging as required.

For more information on Git flow, check out [Atlassian's website](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow).

### Merge versus rebase

When working with the two branches `master` and `develop`, it is reasonable to question whether develop should be merged or rebased on master. Rebasing a commit essentially brings a branch up to a particular point in time, thus making the two branches indistinguishable from one another. On the other hand, a merge will create a new commit showing the changes made to the repository, showing the variation in the branch history (git log).

Whilst the two methods can accomplish the same task, it is normally good working practice to only use rebase on non-public branches (i.e. not `master` or `develop`). For such branches, a merge should be used.

For more information on merges versus rebasing, check out [Atlassian's git tutorial website](https://www.atlassian.com/git/tutorials/merging-vs-rebasing).
