---
layout: post
title: Secure Node.js Development on Linux
date: 2022-07-04
categories: node
slug: secure-development
---
Node.js is an incredibly popular and extremely useful technology, and it comes with a great community that contributes to a vast ecosystem of awesome, open-source packages. This system, however, has its flaws. **Serious security flaws.** This article will offer advice to help mitigate them.

The **Linux platform security** advice contained within this article can be applied to other languages and package management systems but is aimed mostly at Node and NPM as they are, shall we say, *extensively critiqued.*

![Comparing node_modules density to that of the sun or a black hole](/img/node_modules-meme.png)

## The Problem

The main crux of the issue lies in the fact that the Node.js runtime allows virtually unrestricted access to its APIs, and if you're a Node.js developer then you'll know that you can do anything from modifying the filesystem to making HTTP calls. Combine this with the fact that you can install any number of third-party dependencies, which in turn can install any number of third-party dependencies (and so on,) and you have a bit of a supply chain nightmare on your hands. You can't possibly audit the source codes of the hundreds or *thousands* of modules in your `node_modules` folder, can you?

To make matters worse, the developers of a package can, very easily, add an install script to their package. This is useful and there are many valid reasons to do so, but what if that install script executes `rm -rf /`? That's right, you don't even need to *use* the module, you ruin your day by simply *installing* it.

## What can I do?

The best solution is probably to develop in a VM or container dedicated to your project. You could acquaint yourself with [Docker](https://www.docker.com/), which is a popular containerisation system that is often used with Node applications. Avoid storing extraneous data on the container like AWS keys and other secrets, and avoid opening ports and giving it access to your outer network unless necessary. Allow it permission to do only what it needs.

It's also worth noting that you should take care when selecting and using third-party packages, since that's where most of the security concerns originate. Here's some advice to follow in that regard:

- **Don't install a package for every little piece of functionality you need.** Do you really need yet another package to implement that sorting algorithm? Aim for minimalism. If implementing it yourself is feasible, consider doing it. Of course, this doesn't apply for things like cryptography, where rolling your own is an extremely bad idea.

- **Choose widely used packages with multiple maintainers.** Open-source is at its best when there are hundreds or even thousands of eyes scrutinising the code. If many people are using a package then it's probably a good sign, and the more people there are contributing to and maintaining a project then it is more likely that security fixes will be pushed out quickly and less likely that a rogue dev can cause havok.

- **Pin your dependencies.** That means don't let NPM install whichever version it chooses and blindly accept new versions. Whenever an update is available, it should be up to you to decide when to apply it to your projects - preferably after checking the diffs and issues, and probably after waiting for a short while to allow for potential problems to make themselves known (for non-critical updates anyway.)

To pin your dependencies to a specific version, go into your `package.json` and change the version format, e.g.:

**Before**

```json
"dependencies": {
    "package": "^1.0.0"
}
```

**After**

```json
"dependencies": {
    "package": "1.0.0"
}
```

## My Method

It's not always convenient to develop in a dedicated VM or use containers. If you are going to use your host system for development, there are still measures you can take to protect it. Unless I need to test things out on another operating system, I'm going to be developing on my Linux machine, so I have began to harden the system using the Unix accounts and file permission systems.

Here's the process I've developed. The idea can be applied to other software on your system, not just to Node and NPM.

### Creating a user account

The first step is to create new user account(s), separate from the one that you are logged in with, to run Node & NPM processes with. Later, we'll grant this user access to the project files it needs.

You may choose to have one user for all of your Node projects, or perhaps you'd like to use separate users for individual projects or groups of projects. It really depends on your data security needs. Either way, choose relevant and descriptive names for your users so that you remember what its for.

For instance, let's make a new user called `node`:

```sh
sudo useradd -m node
```

`-m` tells it to create a home directory for this new user.

Now provide the new user account with a password:

```sh
sudo passwd node
```

### Adding our account to the new user's group

The `useradd` command will also create a user group with the same name and add the new user to it. We're going to add our personal user account - the one we use to log in with, `distroyob` in this example - to their group so that we'll be able to share files with them.

```sh
sudo usermod -aG node distroyob
```

`-a`, when combined with `-G`, means 'append group', so here we've appended `node` to the list of groups that the `distroyob` user belongs to.

**This change usually requires logging out and back in again or a restart before it will be active.**

### Setting Up Projects

This next part is a little tricky. We need to set the file permissions of the files to grant access to both users, however some programs are designed in such a way that they get very picky with file permissions.

I use Git for version control, and it prefers that the project, and its `.git` directory, be owned by the user that is executing the Git commands. You can specify projects as 'trusted' in the global config to get around this, but it's annoying. I also want to prevent the `node` user from accessing or modifying the `.git` directory, just to be safe.

I'm also using NPM with my Node projects, and it tends to run into permission errors if the project's parent directory is not also owned by the user that is executing the commands.

Currently, my favoured way to approach this is to have the project be located in the `node` user's home directory, and then symlink to it from somewhere within my own home directory. This essentially lets the `node` user "own" the project, but gives me a shortcut to it in, for instance, my projects folder.

So let's move a project to `node`'s home directory:

```sh
mv ./myproject /home/node
```

And create a symlink with an absolute path in its place:

```sh
ln -s /home/node/myproject
```

### Changing file ownership

Now, let's use the `find` command to recursively change the ownership of the project and its files.

First, we can `cd` into the project directory in its new location:

```sh
cd /home/node/myproject
```

Let's change it so that all of the project files and directories are owned by the account that we are logged in as **but belong to the `node` user group that was set up earlier**:

```sh
sudo find . -exec chown distroyob:node {} \;
```

I don't want the `.git` directory and its contents, nor any IDE config directories, being accessible to the `node` user group. I want them accessible only to my user account, so let's recursively change a directory's group to my `distroyob` user group:

```sh
find .git -exec chgrp distroyob {} \;
```

### Using the setgid bit

Okay, that's great. The files' ownership and group is set correctly for now, but what happens when we create new files? Those new files' group is automatically going to be set to our personal user group, not `node`, right?

That's where the **setgid bit** comes in. We can apply this to any directory where we want anything created within it to **always** use the same group as it's parent directory. Let's apply it to the project root and `.git` directories:

```
chmod g+s . .git
```

### Setting project file permissions

Finally, let's change the permissions of each file and directory so that they can be used by the owner and group only:

```sh
find . -type d -exec chmod 770 {} \;
find . -type f -exec chmod 660 {} \;
```

Some files need to be executable. For instance, the scripts inside `.git/hooks`. Let's make them executable:

```sh
chmod 770 .git/hooks/*
```

You can include the following in your `~/.bashrc` file to make these your default file permissions:

```
umask 007
```

### Executing commands as another user

When all is said and done, I'd recommend deleting the `node_modules` folder and reinstalling everything.

You can delete `node_modules` recursively like so:

```sh
rm -dr node_modules
```

Now to execute a command as the `node` user. Let's use `sudo` to execute `npm install` as the `node` user:

```sh
sudo -u node npm install
```

If you wanted to execute more than one command as that user, you can use the switch user command `su`:

```sh
su node
```

After entering that user's password, you're essentially logged in as that user for the remainder of that shell session or until you use the command `exit`.

### The result

After going through this process and running `ls -al` on the project directory, you should have something like this:

![Project directory listing after following the guide](/img/node-secure-dev-ls.png)

Note that `aidan` is my account, and I used `dev` as my unprivileged user, not `node`.

Now go forth and develop in safety! Remember to execute Node and NPM scripts as the new user you created!

## Conclusion

It requires a little administration, but if you set your projects up this way, it reduces the damage that a rogue package can do to the system, and makes your data that little bit more secure.

Your Node processes will run using an unprivileged account that cannot `sudo`, and that only has permissions to access or modify certain files. It can't `rm -rf /`. It can't even access the `.git` folder that we configured the permissions on. It's on you to ensure that the data on your system uses permissions effectively.

Hopefully, this article will have helped you to develop Node applications with a little bit more peace of mind.
