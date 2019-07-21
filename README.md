# multiple-account-git-ssh-setup

-or-

# How to Effectively Manage Remote `git` Repos Across Multiple Accounts and Services

[![bencodes.blog](https://bennowak.github.io/images/favicon.png)](https://bencodes.blog)

[![license](https://img.shields.io/github/license/:bennowak/:multiple-account-git-ssh-setup.svg)](LICENSE)
[![standard-readme compliant](https://img.shields.io/badge/readme%20style-standard-brightgreen.svg?style=flat-square)](https://github.com/RichardLitt/standard-readme)

> If you manage multiple remote repositories across various remote repository hosting services with multiple accounts, this solution should help keep your dev mind a bit more sane!

## Table of Contents

- [Security](#security)
- [Background](#background)
- [Install](#install)
- [Usage](#usage)
- [API](#api)
- [Contributing](#contributing)
- [License](#license)

## Security

While there are no significant security concerns that stand out with the general use of the described configuration, all users must assume personal responsibility for the security of their own systems. If you are using this on a system not owned by you, or a work machine, please be sure you are following all of your organizational guidelines and security best practices.

## Background

Many developers are tasked with juggling dozens of repos that may exist on multiple services and potential multiple accounts within a single service. Utilizing standard SSH public/private key-pair technology reduces the manual labor of authenticating on every interaction with the service and is the 'de facto' mode of connecting and utilizing remote Git repositories.

Previously, the only way to manage this was to set a local configuration file within each repository. While that might be fine for a handful of repos, this doesn't scale well when you have dozens of repos across multiple accounts/organizations (personal/work/clients/other) on multiple services (GitHub, BitBucket, Azure, etc.). Maintaining and backing up all those separate configuration files can be a nightmare, especially if you have need to regularly clear out and re-clone various repos.

Managing remote Git SSH authentication with multiple repos, across multiple accounts, and multiple services can be made much simpler by utilizing a specific directory structure and customizing a handful of configuration files.

## ~~Install~~ Setup and Configuration

This solution depends upon knowledge of [directory structure/organization](), passing familiarity with [SSH key-pairs](), basic terminal familiarity, and simple [git configuration]().

### Step 0 - Preliminary Assumptions

The following are a set of basic assumptions about the system environment that are relevant to this setup.

1. Your various remote git service accounts have the relevant SSH keys registered and working already.
   - _public/private key-pairs have been generated for each account_
   - _the public keys have been registered with the relevant host of that account (GitHub, BitBucket, etc...)_
2. Your machine's `ssh agent` has the keys added
3. You know where to find the `config` file for `ssh`
4. You are comfortable messing around in the terminal environment
5. You are not a complete beginner with `git`

_NOTE: If you don't know the difference between `git` and GitHub, then you should aquire that knowledge (and perhaps a bit more familiarity with the above tools/procedures before moving on with this setup._

### Step 1 - "One `dir` to Rule Them All!"

Settle on a specific location to keep all of your Git repositories. This can be anywhere on your machine. For simplicity, I will demonstrate the use of a single directory in a user's "home" folder (or directory.) For example, we will create a single folder to house a collection of subfolders which will in turn house **ALL** repos. Create a folder called `src` (or whatever you want to call it) in your home folder:

```
~/src/ # On 'linux-like' machines (macOS, Ubunutu, etc.)
\Users\username\src\ #(Windows 7/8/10)
```

### Step 2 - "Sub-divide an Conquer"

At this point you now have a central place to find all your code. Well enough, but now you have a choice to make about how you will organize your sub-directories as "bins" to house multiple repos in regards to accounts and services. There are basically three obvious options with different considerations (pros and cons).

#### Option 1 - Clear and Concise

- **`src/service-name/account/repos`**

I like this option the most because it lends itself to succinct and clear configuration files and separation of concerns. An example structuring method looks like this

```
~/src/
    |___ github.com/
    |       |
    |       |___ personal-acct-main/
    |       |       |___ .gitconfig
    |       |       |___ repo-001/
    |       |       |___ ...
    |       |       |___ repo-101/
    |       |
    |       |___ personal-acct-other/
    |       |       |___ .gitconfig
    |       |       |___ repo-001/
    |       |       |___ ...
    |       |       |___ repo-101/
    |       |
    |       |___ org1-acct/
    |       |       |___ .gitconfig
    |       |       |___ repo-001/
    |       |       |___ ...
    |       |       |___ repo-101/
    |       |
    |       |___ org2-acct/
    |               |___ .gitconfig
    |               |___ repo-001/
    |               |___ ...
    |               |___ repo-101/
    |
    |___ bitbucket.com/
    |       |
    |       |___ org1-acct/
    |       |       |___ .gitconfig
    |       |       |___ repo-001/
    |       |       |___ ...
    |       |       |___ repo-101/
    |       |
    |       |___ org2-acct/
    |               |___ .gitconfig
    |               |___ repo-001/
    |               |___ ...
    |               |___ repo-101/
    |
    |___ some-other-remote-repo-hosting-service.com/
            |
            |___ org1-acct/
            |       |___ .gitconfig
            |       |___ repo-001/
            |       |___ ...
            |       |___ repo-101/
            |
            |___ org2-acct/
                    |___ .gitconfig
                    |___ repo-001/
                    |___ ...
                    |___ repo-101/
```

The simple takeaway for this option is that (according to this contrived example) there are only really eight super-small (5 lines), very simple configuration files that allow me to auto-magically allow me to use remote git functionality across 808 repositories with no logins, credential switching, or auth challenges. Imagine having to manage, configure and individually backup more than a couple handful of repo configs.

Structuring things this way means that there is only a very basic `.gitconfig` file in each account sub-folder. If you have a single project that has multiple repos across accounts and using multiple services, this does mean you might have to remember where all the different repos are. However, I find this to be an edge case and not relevant to most workflows.

#### Option 2 - Separated by Concern/Category/Project

- **`src/concern-category-project-name/service-name/account/repos`**

This is a potentially viable option. Complications arise when you have different projects (separate sub-directories) that have repos that use the same remote hosting service.

```
~/src/
    |___ project-01/
    |       |
    |       |___ github.com/
    |       |       |
    |       |       |___personal-acct-main/
    |       |       |       |___ .gitconfig
    |       |       |       |___ repo-001/
    |       |       |       |___ ...
    |       |       |       |___ repo-101/
    |       |       |
    |       |       |___personal-acct-other/
    |       |               |___ .gitconfig
    |       |               |___ repo-001/
    |       |               |___ ...
    |       |               |___ repo-101/
    |       |
    |       |___ bitbucket.com/
    |               |
    |               |___ org1-acct/
    |                       |___ .gitconfig
    |                       |___ repo-001/
    |                       |___ ...
    |                       |___ repo-101/
    |___ project-02/
    |       |
    |       |___ github.com/
    |       |       |
    |       |       |___personal-acct-main/
    |       |       |       |___ .gitconfig
    |       |       |       |___ repo-001/
    |       |       |       |___ ...
    |       |       |       |___ repo-101/
    |       |       |
    |       |       |___personal-acct-other/
    |       |               |___ .gitconfig
    |       |               |___ repo-001/
    |       |               |___ ...
    |       |               |___ repo-101/
    |       |
    |       |___ bitbucket.com/
    |               |
    |               |___ org1-acct/
    |                       |___ .gitconfig
    |                       |___ repo-001/
    |                       |___ ...
    |                       |___ repo-101/
    |___ project-03/
            |
            |___ github.com/
            |       |
            |       |___personal-acct-main/
            |               |___ .gitconfig
            |               |___ repo-001/
            |               |___ ...
            |               |___ repo-101/
            |
            |___ bitbucket.com/
                    |
                    |___ org1-acct/
                            |___ .gitconfig
                            |___ repo-001/
                            |___ ...
                            |___ repo-101/
```

Here we have the same number of repositories as option 1. And we also have the same number of config files (assuming were only ever working on 3 projects and not more than a handful). On the surface one might think there's little to no difference. However if we were to analyze it, we are actually duplicating identical configuration files. For example the `.gitconfig` files in :

- `project-01/github.com/personal-acct-main`
- `project-02/github.com/personal-acct-main`
- `project-03/github.com/personal-acct-main`

are all exactly the same. What happens if we need to edit the configuration related to `personal-acct-main`'s `github.com` account? What if we move the repo to a different provider? If we had used **Option 1** we would only have 3 configuration files to manage. Think about what happens when we scale to tens, or even hundreds, of separate projects. We would have a maintenance fiasco in the making.

#### Option 3 - There be Dragons Here!

- **`src/some/arbitrary/structure/repos`**

I won't waste the time displaying an example for this one, but do this at your own assumed risk to your mental sanity.

#### _Some Thoughts about the Options_

Admittedly the last two options are a bit contrived, but they serve to demonstrate how scaling issues will creep in and make management less efficient. Do whatever works for you using this guide as, ... well ..., a guide.

### Step 3 - Editing and Creating Configs

The key components and relevant parts to making this whole system work are

1. Individual `.gitconfig` files located in the appropriate subdirectories that supply
   - `user.name`
   - `user.email`
   - A `url` directive that maps remote `git` commands to use a specific `Host` entry in the `ssh` configuration file
2. Some minor customization of your system's `ssh` configuration file to map the custom `url` directives in the various `.gitconfig` files to use account specific `ssh` keys
3. `[includeIf]` entries in the global `git` config file to that dynamically insert the account-specific `.gitconfig` info based upon what directory a particular `git` command is run in

Let's get started ...

#### 1) Account `.gitconfig` Files

Create this file at the root of each "account" level sub-directory. For example: if you have multiple accounts on GitHub (possibly `usernameA` and `usernameB`), then to configure `usernameA`, the `.gitconfig` file would be created using the following directory/sub-directory structure.

`~/src/github.com/usernameA/.gitconfig`

Be sure to copy and past the template below into the `.gitconfig` file you just created. Then edit all the places you see surrounded by angles (`<` `>`) and replace them with the appropriate information. Don't leave the angles in there. They are just for visual reference in this template.

```
[user]
    name = <First Last>
    email = <username@email-service.com>

[url "git@github.com_<account_identifier_suffix>"]
    insteadOf = git@github.com
```

Do this for each account in each service.

_! **IMPORTANT NOTE:** `<_account_identifier_suffic>` above needs to be replaced with something that uniquely identifies the account. I generally use the account username, but it can be anything that makes sense to you. The url value inside the double quotes will be used in the `ssh` `config` file as `Host` entries._

#### 2) System SSH `config` File Customization

In your system's SSH `config` file (mine's located at `~/.ssh/config` no extension on the filename) add `Host` entries for each account using the following as a guide.

```
Host github.com_<account_A_identifier_suffix>
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_usernameA
    User git

Host github.com_<account_B_identifier_suffix>
    HostName github.com
    IdentityFile ~/.ssh/id_rsa_usernameB
    User git
```

You should have an entry similar to the one below (assuming you haven't already done anything funky with your SSH confg). Just leave it as the last entry. This serves as the "default" `ssh` configuration. Other services rely on this entry so just leave it alone.

```
Host *
    GSSAPIAuthentication yes
    GSSAPIDelegateCredentials yes
    ForwardAgent yes
    AddKeysToAgent yes
```

#### 3) `[includeIf]` Entries in User's `.config` File

Finally is the glue that marries the previous two parts together and gives us remote repo management bliss. It's been a long journey, but it will be so worth it!

Open the "user" level `.gitconfig` file located in your home directory. (Should be at `~/.gitconfig`). If you don't have one there, just create one. This is where your personal customizations to `git` live. I have a ton of customizations in it, and I've got a pretty suite (my opinion) setup. If you wan't to check out how I've customized my `git` workflow then checkout this [`gist`](https://gist.github.com/bennowak/asdf).

Use the following template to add entries for each account.

```
[includeif "gitdir:~/src/github.com/<usernameA>/"]
	path = ~/src/github.com/<usernameA>/.gitconfig

[includeif "gitdir:~/src/github.com/<usernameB>/"]
	path = ~/src/github.com/<usernameB>/.gitconfig
```

Having completed all those steps, the correct `ssh` credentials (key-pair) will automatically be retrieved and used depending on which directory you are in.

## Usage

Assuming you've accepted my argument for using **Option 1**, all you need to do is clone and/or create your repos into/in the relevant account-level directories. You can create further sub-directories within the account sub-directory if you want to, but I haven't found it necessary. That's it! Enjoy.

### Explaining the Magic (No Hand Waving Here!)

So here's what's going on. I'll use a SSH-style `git clone <url>` command (on this repo) to demonstrate.

1.  `git@github.com:bennowak/multiple-account-git-ssh-setup.git` is run from the terminal inside the directory `~/src/github.com/bennowak` which contains the account-level `.gitconfig` file.
2.  `git` checks the `.gitconfig` file in my home directory and parses the various `[includeIf]` directives to see if it should pull in configuration settings from other files depending on which directory the original `git` command was run in (the working directory).
3.  Because the working directory matches a conditional the `path` argument is used to find the `.gitconfig` and incorporate those settings in the current process
4.  The "fetched" settings include the `user.name`, `user.email` data which is used for the duration of the process. Additionally the `[url]` directive causes the request's `url`to be transformed from `github.com` into `github.com_bennowak`
5.  Because I'm using SSH mode (inferred by the `git clone` url argument's structure) the `ssh` agent supplies the `IdentityFile` that is listed under the `Host` entry that matches the request url `github.com_bennowak`
6.  Viola! The process of authenticating multiple SSH identities based on which directory I happen to be in is effectively automated and everything is right with the world.

## API

There's no API per se. However I am toying around with a bash script to scaffold this out. Ping me on github if that interests you.

## Contributing

See [the contributing file](CONTRIBUTING.md)!

PRs accepted.

Small note: If editing the Readme, please conform to the [standard-readme](https://github.com/RichardLitt/standard-readme) specification.

## License

[©Ben Nowak](LICENSE)
