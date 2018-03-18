# Git Theory

> Git is a **DVCS**, originated for Linux Kernel development

## Git CLI

Implemented in:
- C
- Java
- Ruby
- Python

## Git Workflow and Key Concepts

| Working Tree | Staging Area | Local Repo | Remote Repo |
|:------------:|:------------:|:----------:|:-----------:|
| Make changes to the files based on the previous **repo state** | Define the new **repo state** based on the changes in the **Working Tree** | Store the what was defined in the **Staging Area** into an immutable **Commit** in the **Local Repo** | Share the updated repository with another **Remote Repo** |


## Git Repository

Git repositories can be differentiated
- by Function
  - for **work**
    - a **work** Git repository initialized by default with the `init` command
    - meant for human interaction
    - located in the `./git/` directory
    - the `./` directory is the **working tree**
  - for **sharing**
    - a **bare** Git repository initialize with the `--bare` argument
    - automated to a certain degree to create a *share* point
    - located in the `./` *root* directory
    - no **working tree**
- by Location
  - **local**
    - initialized locally
    - *usually* a **work** repo
  - **remote**
    - initialized on a remote location
    - *usually* a **bare** repo

## Git Internals

```bash
git --version;

git --help;
```

### Git Repository structure
```bash
echo "
.
└── .git/
    ├── HEAD
    ├── branches/
    ├── config
    ├── description
    ├── hooks/
    │   ├── applypatch-msg.sample
    │   ├── commit-msg.sample
    │   ├── post-update.sample
    │   ├── pre-applypatch.sample
    │   ├── pre-commit.sample
    │   ├── prepare-commit-msg.sample
    │   ├── pre-push.sample
    │   ├── pre-rebase.sample
    │   └── update.sample
    ├── info/
    │   └── exclude
    ├── objects/
    │   ├── info/
    │   └── pack/
    └── refs/
        ├── heads/
        ├── remotes/
        └── tags/
";
```

### Git Commit structure
```bash
git cat-file commit HEAD;
```

### Git Commit ID
```bash
git log -n2 HEAD;

git rev-parse HEAD;

(printf "commit %s\0" $(git cat-file -p @ | wc -c); git cat-file -p @) | sha1sum --tag;
```