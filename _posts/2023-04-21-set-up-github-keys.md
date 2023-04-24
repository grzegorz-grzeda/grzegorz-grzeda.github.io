---
layout: post
title: "Set up github keys"
date: 2023-04-21 07:00:00 -0000
categories: git
---

When I want to set up my new Linux environment for using my GitHub resources, I need to set up a secure key to authenticate.

I set separate key for each environmnent, minimising the possibility of breach.

## Local set up
I generate a new key, using my email, by which I log in to github:

```bash
$ ssh-keygen -t ed25519 -C "your_email@example.com"
```
On prompt, I use the default settings, regarding file location and I don't set up the passphrase (my computer passwords are secure enough to store those keys safely).

Next, I check if the `ssh-agent` is running (example response):
```bash
$ eval "$(ssh-agent -s)"
> Agent pid 59566
```

If so, I add my fresh generated key to the `ssh-agent`. Here, as the password file was generated with default name, I add it as so:
```bash
$ ssh-add ~/.ssh/id_ed25519
```

## Adding key information to github account
I copy the content of the public part of key to clipboard (using default file name):
```bash
$ clip < ~/.ssh/id_ed25519.pub
```

Next, I do:
1. head to my github account
2. __Settings__
3. __SSH and GPG keys__
4. __New SSH key__ or __Add SSH key__
5. In `Title` add some relevant description
6. Paste the key public part content from clipboard to __Key__ area
7. Click __Add SSH Key__
8. When asked authenticate with GitHub account.

Now I can enjoy my new environment able to access my github content.