# Git
## Types of Version Control
Version control systems are used to track changes in one or multiple files, enabling easy retrieval of specific revisions in the future. During software development, version control systems help in creating backups of every modification made, allowing easy navigation to any previous version. There are different types of version control systems, broadly categorized as: local version control systems, centralized version control systems, and distributed version control systems.
### Local Version Control System
Many people are accustomed to saving different versions of a project directory by simply copying the entire directory and perhaps renaming it with a timestamp for differentiation. The only advantage of this approach is its simplicity, but it is prone to mistakes. Sometimes, people can get confused about their working directory and accidentally write or overwrite files in the wrong place. To address this issue, various local version control systems were developed long ago, most of which use a simple database to record the differences between file updates over time.

One of the most popular systems is called RCS (Revision Control System), and traces of it can still be found on many computer systems today. Even on popular operating systems like Mac OS X, after installing the developer toolkit, you can use RCS commands. The way it works is by storing a set of patches on the hard drive (patches refer to the changes between file revisions). By applying all the patches, the contents of each version of the file can be reconstructed.

### Centralized Version Control Systems
Next, people encountered another problem: how to enable collaboration among developers on different systems? Thus, Centralized Version Control Systems (CVCS) came into existence. Systems like CVS, Subversion, and Perforce fall into this category. They have a central, managed server that stores all the revision versions of files, and collaborators connect to this server through clients to retrieve the latest files or submit updates. Over the years, this has become the standard practice for version control systems.

This approach brings many benefits, especially compared to older local VCS. Everyone can to some extent see what others are working on in the project, and administrators can easily manage the permissions of each developer. Additionally, managing a CVCS is much easier compared to maintaining local databases on multiple clients. The most obvious drawback of this approach is the single point of failure with the central server. If the central server goes down for an hour, during that time, no one can submit updates or collaborate. If the disk containing the central database gets damaged without timely backups, you will undoubtedly lose all data, including the entire change history of the project, leaving only individual snapshots preserved on people's machines. Local version control systems also face similar issues, as long as the entire project history is stored in a single location, there is a risk of losing all historical update records.

### Distributed Version Control Systems
To avoid the risk of a single point of failure in centralized version control systems, Distributed Version Control Systems (DVCS) emerged. Systems like Git, Mercurial, Bazaar, and Darcs fall into this category. In DVCS, clients don't just extract snapshots of the latest version of files; instead, they mirror the entire code repository. This means that if any server used for collaboration fails, you can recover using any of the locally mirrored repositories. Every clone operation effectively creates a full backup of the code repository.

Furthermore, many of these systems allow interaction with multiple remote code repositories. This enables collaboration with different teams within the same project. You can set up different collaboration workflows as needed, such as hierarchical models, which were not possible in previous centralized systems.

## Concepts
### Snapshot Stream
The main difference between Git and other version control systems lies in how Git stores data. **Conceptually, most other systems store information as a list of file changes.** These systems, such as CVS, Subversion, Perforce, Bazaar, etc., view the stored information as a collection of accumulated file differences over time.

Git, on the other hand, doesn't store data in the same way. Instead, **Git treats data as a set of snapshots of a miniature filesystem.** Each time you commit updates or save the project state in Git, it creates a snapshot of all the files and saves an index of that snapshot. For efficiency, if a file hasn't been modified, Git doesn't re-store the entire file but instead keeps a link to the previously stored file. Git's method of storing data is more like a snapshot stream.

### Local Operations
The majority of operations in Git only require accessing local files and resources, without needing information from other computers on the network. Compared to the significant network latency in centralized version control systems, working with Git locally is much faster. Since you have the complete history of the project on your local disk, most operations appear to be instantaneous.

For example, when browsing the project's history, Git doesn't need to connect to a server to fetch the history and display it. It can directly read from the local database, allowing you to immediately see the project's history. If you want to see the changes between the current version and a version from a month ago, Git will perform a local diff calculation using the file from a month ago, rather than relying on a remote server or fetching the old version file from the server and processing it locally.

This also means that you can perform almost all operations even when you're offline. For instance, if you want to work on a plane or train, you can happily make commits and upload them when you have a network connection. If your home VPN client is not working properly, you can still work. With other systems, achieving this level of functionality offline is either impossible or very challenging. For example, with Perforce, you can hardly do anything when you're not connected to the server. With Subversion and CVS, you can modify files, but you cannot commit the changes to the database (since your local database is offline). This may not seem like a big issue, but you'll be pleasantly surprised by the significant difference it makes.

### Data Addition in Git
The operations we perform in Git fundamentally involve adding operation data to the Git database. Almost all operations in Git are reversible. Like other version control systems, uncommitted updates can be lost or mixed up with other modifications. However, once you commit the modified snapshot to the Git system, it becomes difficult to lose the data. This makes Git an excellent tool in the field of version control because it allows us to make various modifications and experiment freely while still having the opportunity to revert back if needed.

### Data Integrity in Git
In Git, all data is checksummed before it is stored, and it is referenced by the checksum. This means that it is impossible to change any file or directory content without Git being aware of it. This feature is implemented by Git at its core. If you lose information or damage files during the editing process, Git can detect it.

Git uses the SHA-1 hash algorithm to calculate the checksum. Git calculates the hash value of the file's content or directory structure, ensuring the integrity of files and paths. There are many cases where Git utilizes these hash values, and you will often come across them. In fact, the information stored in the Git database is indexed by the hash value of the file's content, not the file name.

### Working Directory and Git Staging Area
Git has three states in which your files can reside: committed, modified, and staged. Committed means that the data is safely stored in the local database; modified means that the file has been changed but not yet committed to the database; staged means that the modified file has been marked to be included in the next commit's snapshot.

This introduces the concept of three working areas in a Git project: working directory, Git repository, and staging area. The Git repository includes the local repository and the remote repository.

Working Directory: This is the directory where you directly edit and modify your project. The working directory is where you extract a specific version of the project's content from the repository onto your disk for use or modification.

Git Repository: The Git repository stores the project's metadata and object database. This is the most important part of Git. When you clone a repository from another computer, you are copying the data from this repository.

Staging Area: The staging area is a file that stores information about the files to be included in the next commit. It is generally located in the Git repository. Sometimes it is also referred to as the "index," but it is commonly known as the staging area.

By defining these three work areas, the workflow is very clear. The basic Git workflow consists of the following steps:
1. Modify files in the working directory.
2. Stage the files by taking snapshots of them and placing them in the staging area.
3. Commit the updates by taking the files from the staging area and permanently storing the snapshots in the Git repository.

If a specific version of a file is stored in the Git repository, it is considered to be in the committed state. If a file has been modified and staged, it is in the staged state. If a file has been modified since it was last checked out but has not been staged yet, it is in the modified state.

### Branching
To understand how Git implements branching, let's review how Git stores data. Git doesn't save file differences or changes; instead, it stores a series of file snapshots. When you make a commit in Git, it saves a commit object that includes a pointer to the snapshot of the staged content. It also contains information about the commit, such as the author and any parent commit pointers. Initially, the first commit has no direct ancestors, regular commits have one ancestor, and merge commits resulting from merging multiple branches have multiple ancestors.

For the sake of clarity, let's assume there are three files in the working directory, and we want to stage and commit them. The staging operation calculates the checksum for each file and saves the current version of the files as snapshots in the Git repository (Git uses blob objects to store these snapshots). It also adds the checksums to the staging area.
```
git add README test.rb LICENSE
git commit -m 'initial commit of my project'
```
Before creating a new commit object with `git commit`, Git calculates the checksum of each subdirectory (in this case, the project's root directory). It then saves these directories as tree objects in the Git repository. The resulting commit object contains the commit information and a pointer to the tree object (representing the root directory) so that it can reproduce the snapshot's content in the future when needed.

Now, the Git repository contains 5 objects: 3 blob objects representing the file snapshot contents, 1 tree object that records the directory tree contents and the corresponding blob object indexes, and 1 commit object that contains an index to the tree object (representing the root directory) and other metadata about the commit.

When you make further modifications and commit again, the new commit object will include a pointer to the previous commit object.

Now let's talk about branches. In Git, a branch is essentially a mutable pointer to a commit object. The branch named "master" is the default branch in Git. The first branch is often referred to as the "main" branch. The main branch can be cloned to create other branches, and the mutable pointers of each branch automatically move forward with each commit.