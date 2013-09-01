---
layout: post
title: Simple backups with Git
original_link: https://gist.github.com/3265686
---


# Simple backups with Git
Git calls itself "stupid content tracker", which means that the core just stores any data you put in and allows you to get it back. On top of that, there's all the fancy stuff like branching, diffs, remote repos etc, but that's not important for us today. We just want to **track content**, and the more *stupidier* (i.e. **simple**) way, the better - it'll be simpler to do any crazy stuff we imagine later on.


## Motivation
I wanted to **do backups** of couple of terabytes of data, consisting of quite small files (< 10MB) which usually don't change. I wanted to have **incremental** backups (not to transfer all data) and I wanted to see **history** and be able to get into **any version**.

So why not to use git for it?

It's pretty popular and there are soo many tools for it, which we would be able to exploit. With [git-fs](https://github.com/g2p/git-fs/) you can *mount* a git repo and browse thru it as it was a normal filesystem. With simple *git push* you can store backups on another machine easily. With [gitk](http://lwn.net/images/ns/kernel/gitk.png) you can see the history in GUI. With [gitweb](https://git.wiki.kernel.org/index.php/Gitweb) you can browse contents of your backups in a web browser. And I hope you'll come with many more awesome ideas.


## Existing tools
Some people already has this idea and implemented it.

### bup
[bup](https://github.com/apenwarr/bup/) is (quote) *highly efficient file backup system based on the git packfile format. Capable of doing fast incremental backups of virtual machine images.* I played with it for a while, but didn't like it very much. For several reasons it doesn't store files directly to git, but wants them joined by *tar* and stores raw chunks. Thus, when you explore repo contents, you will NOT see your files directly. Thus, you won't be able to use the whole git ecosystem with it :(

### gibak
[gibak](https://github.com/pangloss/gibak) is just a very small wrapper for git, which just simplifies some commands or runs more at once. It looks fine, but tries to do little magic. I'll rather be explicit to avoid any WTF effects. Also I've got some specific needs which would probably bring more WTFs with a tool I don't know.



## Plain git
Eventually I decided to use *plain old git*. It has been tested by millions of users, so I don't need to worry much about potential bugs. And I already know it pretty well, so I'm sure it'll do what I tell it to do. Lots of experience = less WTFs.

Let's start. First, I want to have the repo on a separate disk.
```bash
export GIT_DIR=/media/2tb/git-backup
git init
```

When having a repository *detached* from the *working directory*, git assumes it to be *bare* and won't allow you to add anything to it normally. We can change it.

```bash
git config core.bare false
```

Because I want to store some gigs or even terabytes of data, I'd like to avoid *garbage collection* which may be executed by default and which tries to compress/clean the data. When I want it, I'll run it explicitly.

```bash
git config gc.auto 0
```

We're ready to start with our backups. Because we will use git in a very simple fassion (only as a *stupid content tracker*, we can do some nasty tricks. For example, we can back up different directories from different parts of the filesystem. You'll see it in a moment.

```bash
cd ~/my-app/data
time git add mail/2012
time git commit -q -m "first part of mail"
```

This may take a couple of minutes or even hours, if you've got lots (gigs) or data in your mail archive. *git add* will just add all the files and outputs nothing. *git commit* normally outputs a summary, which may be extremely long - we will omit it by `-q` option (quiet). *time* is just for some brief info on how long this backup took.

Suppost we want to back up something else now, e.g. scanned documents, which resides in different part of filesystem. No problem:
```bash
cd /home/xyz
git add scan
git commit -q -m "scans"
```

Again, git will simply add all the files. If they already exist, git will behave as you'd expect to - keep them if unchanged or add new version otherwise. Just like everytime you used git elsewhere.


## git status
At this time, git repo containts structure like this:
```
mail/
  2012/
    ... lots of files
scan/
  2011/ ...
  2012/ ...
```

Notice, that `mail/` and `scan/` directories do not exists near each other in anywhere in the filesystem, but just in git. If you'd ask for `git status`, it would tell you that a huge load of files have been removed. Which is not what we want. We need to specify what directory we want to see status for.
```bash
cd /home/xyz
git status scan
```

This will show *status* only for the relevant directory. We don't care about other ones at the moment.


## git add
Similar rules apply to other git commands like `git add`. You can not *commit all* (git commit -a), because it would delete the *mail files* when executed in *scan directory* and vice versa. You have to tell git, which directory to add. Remember, **we use git stupidly!** 
```bash
cd /home/xyz
git add scan
git commit -m "more scans"
```



## Smart tools
Because we use a normal git repository, we can use all the amazing tools created for git.

### git fs
Do you want to see data as they were some time ago? Mount your repo with [git-fs](https://github.com/g2p/git-fs/) and you'll have each branch/tag/commit as a normal directory in your filesystem. Of course just a *virtual* one, it won't extract anything nor use any space on disks.



## Gotchas
We use git in a somehow specific manner, so be careful not to break anything.

### unset GIT_DIR
After you're finished with your backups, remember to `unset GIT_DIR` or close the console. Otherwise, git will keep use the backup repository for everything you do. It'll have precedence, even if you're in a working directory of another repo.

### git's limits
I heard somewhere Linus one said git was not best for huge repos. But *bup* and *gibak* say it's fine, though probably not for all possible operations. Anyway, if you use it just as a stupid content tracker, you shouldn't get into performance issues.



## Summary
Backups with git seemed to work fine in my testing environment (with some gigs of data - music, photos, books). I'm now trying it in production to back up almost 2TB of data and I'll update this gist with my experience. It goes slowly and I'll be adding more and more directories until I've got backed up everything.

