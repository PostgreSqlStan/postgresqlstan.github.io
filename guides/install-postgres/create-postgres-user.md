---
title: Create a postgres user on macOS
date: 2021-12-29
last_modified_at: 2021-12-29
sidebar:
  nav: guides
author_profile: false  
---

The generic Unix command to create a postgres user doesn’t work on a Mac:

```shell
% adduser postgres
zsh: command not found: adduser
```

The Directory Service utility `dscl` is used on macOS to manage user accounts.

To check if a postgres account already exists:

```shell
dscl . -read /Users/postgres
```

If there’s no postgres account, you should see an error like this: `<dscl_cmd> DS Error: -14136 (eDSRecordNotFound)`.

{% capture notice-1 %}
If your computer already has a postgres account, it’s probably OK to use that account for a fresh installation of postgres, but I can’t make any guarantees.
{% endcapture %}

<div class="notice">{{ notice-1 | markdownify }}</div>

## Create a postgres group and user

Create a postgres group:

```shell
sudo dscl . -create /Groups/postgres
sudo dscl . -create /Groups/postgres PrimaryGroupID 1000
sudo dscl . -create /Groups/postgres RealName "PostgreSQL"
```

Create a postgres user:

```shell
sudo dscl . -create /Users/postgres
sudo dscl . -create /Users/postgres NFSHomeDirectory /var/empty
sudo dscl . -create /Users/postgres Password "*"
sudo dscl . -create /Users/postgres PrimaryGroupID 1000
sudo dscl . -create /Users/postgres RealName "PostgreSQL Server"
sudo dscl . -create /Users/postgres UniqueID 1000
sudo dscl . -create /Users/postgres UserShell /usr/bin/false
```

### Check the postgres user/group 

You can use the `-read` option to check the results:

```shell
dscl . -read /Groups/postgres
dscl . -read /Users/postgres
```

---
[Next: Post-installation steps](post-install.md){: .btn .btn--info}


