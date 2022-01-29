---
title: Create a postgres user on macOS
date: 2022-01-29
sidebar:
  nav: guides
classes: wide
---

If you follow the directions in the documention, you'll run into a problem because the command to create a new user doesn’t work on macOS:

```
% adduser postgres
zsh: command not found: adduser
```

## Use dscl to create postgres user and group

The Directory Service utility `dscl` is used on macOS to manage user accounts.

To check if a postgres user already exists:

```
% dscl . -read /Users/postgres
```

If there’s no postgres account, you should see an error.

{% capture notice-1 %}
If your computer already has a postgres account, it’s probably OK to skip this step and use that account for a fresh installation of postgres, but I can’t make any guarantees.
{% endcapture %}

<div class="notice">{{ notice-1 | markdownify }}</div>

Create a postgres group:

```
% sudo dscl . -create /Groups/postgres
% sudo dscl . -create /Groups/postgres PrimaryGroupID 1000
% sudo dscl . -create /Groups/postgres RealName "PostgreSQL"
```

Create a postgres user:

```
% sudo dscl . -create /Users/postgres
% sudo dscl . -create /Users/postgres NFSHomeDirectory /var/empty
% sudo dscl . -create /Users/postgres Password "*"
% sudo dscl . -create /Users/postgres PrimaryGroupID 1000
% sudo dscl . -create /Users/postgres RealName "PostgreSQL Server"
% sudo dscl . -create /Users/postgres UniqueID 1000
% sudo dscl . -create /Users/postgres UserShell /usr/bin/false
```

## Check the postgres user/group

Use the `-read` option to verify the postgres group and user have been created:

```
% dscl . -read /Groups/postgres
% dscl . -read /Users/postgres
```

---
[Next: Post-installation steps](post-install.md){: .btn .btn--info}


