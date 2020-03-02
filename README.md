# ops-charm profile template

This repo could be used as a template for creating your own ops-charm
environments. 

## ops-charm

For more information of what is ops-charm and how it works go to [its repo](https://github.com/kangco-de/ops-charm/blob/master/README.md).

## How to initialize all ops-charm features


## Prerequisites

Install this dependencies from your repos:

```
virtualenv_wrapper
gpg     # Not needed but recommended
gopass  # Not needed but recommended
chezmoi # Not needed but recommended
```


### Chezmoi

Chezmoi is a tool for version-control all the `dotfile` you want to control.

A good use of this is to keep on your environment or your team all your dotfiles so
all the team has the same configuration and/or aliases.

chezmoi expects, as same a gopass, to have a repo for itself but opposed to gopass
chezmoi uses git cli for everything so you can use chezmoi in a subfolder without any problem.

> NOTE: the only problem with chezmoi in a subfolder is on `init` stage if the repo has a
> `.chezmoi.toml.tmpl` in its root so every `chezmoi init` could result in clning your repo in
> `$HOME/evns/template/chezmoi` subfoler instead of `$HOME/evns/template` so ops-charm environment
> and chezmoi end unusable withou manual intervention. Moving manualliy the folder to its father or
> `rm ~/.config/chezmoi/chezmoi.toml && chezmoi init --source ~/envs/template github.com/you/template`

If you don't want to use chezmoi delete `chezmoi` folder.

If you want to use if you could modify the files on `chezmoi` folder to your needs.

```bash
chezmoi init --source ~/envs/your-new-environment github.com/kangco-de/ops-charm-env-template
```

This should initizalize ops-charm and the new environment in order to chezmoi to template all
files so ops-charm and the new environment could work.

> :warning: **This is not a biiolerplate method for every environment:** chezmoi could destroy
other chezmoi instances work while templating dot_files. I use it this way because it is my use
of case to mantaining a sane group of environments.

### GPG

It is used as a ssh agent and for crypting gopass passwords.

For the agent to be activated `GNUPGHOME` environment variable and a file in
`$GNUPGHOME/sshcontrol` must exist.

`GNUPGHOME` could point to a path that is outside of the environment so the kayring
could be used by more than one environment. For example having and environment for the
team itself with the gopass passwords ans gpg keyring and 3 environments for production,
staging and devel which GPG and gopass variables point to the team environment.

* If you did not used chezmoi to create your environment, make sure you have on your
`.bashrc` the `ENVIRONMENTS` variable exported and clone your new environment on a subfolder
and initialize it:

```bash
cd $ENVIRONMENTS
git clone https://github.com/kangco-de/ops-charm-env-template your-new-environment
mkvirtualenv your-new-environment
workon your-new-environment
pip install -r your-new-environment/requirements.txt
```

Once done, create your new keypair:

```
$ gpg --full-gen-key
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection?
```
1
```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
```
5y
```
Key expires at Sat Mar  1 13:09:55 2025 CET
Is this correct? (y/N)
```
y
```
GnuPG needs to construct a user ID to identify your key.

Real name:
```
Insert Your Name
```
Email address:
```
your@mail.tld
```
Comment: 
You selected this USER-ID:
    "Insert Your Name <your@mail.tld>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
```

A pinentry interface should pop up on your TTY or graphically

```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 08B6C27B68FB5C3E marked as ultimately trusted    
gpg: revocation certificate stored as '/home/kang/envs/dsmctools-team/gnupg/openpgp-revocs.d/5BC149977
37EA46A508DEA5F08B6C27B68FB5C3E.rev'
public and secret key created and signed.
                                                   
pub   rsa2048 2020-03-02 [SC]            
      5BC14997737EA46A508DEA5F08B6C27B68FB5C3E
uid                      Insert Your Name <your@mail.tld>
sub   rsa2048 2020-03-02 [E]
```

Now list your new created key with `gpg --list-keys` and edit it using your hash and edit
you key in expert mode to create an auth key:
```
$ gpg --expert --edit-key 5BC14997737EA46A508DEA5
F08B6C27B68FB5C3E                                   
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb 
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
sec  rsa2048/08B6C27B68FB5C3E
     created: 2020-03-02  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/E0F1FCFB3868F981
     created: 2020-03-02  expires: never       usage: E   
[ultimate] (1). Insert Your Name <your@mail.tld>

gpg> addkey
Please select what kind of key you want:
   (3) DSA (sign only)
   (4) RSA (sign only)
   (5) Elgamal (encrypt only)
   (6) RSA (encrypt only) 
   (7) DSA (set your own capabilities)
   (8) RSA (set your own capabilities)
  (10) ECC (sign only)
  (11) ECC (set your own capabilities)
  (12) ECC (encrypt only) 
  (13) Existing key
  (14) Existing key from card
Your selection?
```
8
```

Possible actions for a RSA key: Sign Encrypt Authenticate 
Current allowed actions: Sign Encrypt 

   (S) Toggle the sign capability
   (E) Toggle the encrypt capability
   (A) Toggle the authenticate capability
   (Q) Finished

Your selection?
```
`s`
`e`
`a`
`q`
```
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 
Requested keysize is 2048 bits
Please specify how long the key should be valid.
         0 = key does not expire                                                                      
      <n>  = key expires in n days    
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years 
Key is valid for? (0)
```
`<Enter>` as we want this key to never expire.
```
Key does not expire at all
Is this correct? (y/N)
```
y
```
Really create? (y/N)
```
y
```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.

sec  rsa2048/08B6C27B68FB5C3E
     created: 2020-03-02  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa2048/E0F1FCFB3868F981
     created: 2020-03-02  expires: never       usage: E   
ssb  rsa2048/D4E401E54A0139E2
     created: 2020-03-02  expires: never       usage: A   
[ultimate] (1). Insert Your Name <your@mail.tld>

gpg>
```
quit
```
Save changes? (y/N)
```
Finally: `y`

Now you have to export it to be used by ssh:
```
$ gpg --list-keys --with-subkey-fingerprint
/home/kang/envs/dsmctools-team/gnupg/pubring.kbx
------------------------------------------------
pub   rsa2048 2020-03-02 [SC]
      5BC14997737EA46A508DEA5F08B6C27B68FB5C3E
uid           [ultimate] Insert Your Name <your@mail.tld>
sub   rsa2048 2020-03-02 [E]
      1401A808E83EF4BF6BB99360E0F1FCFB3868F981
sub   rsa2048 2020-03-02 [A]
      50709C634ADEA8380B072A04D4E401E54A0139E2

$ gpg --export-ssh-key 50709C634ADEA8380B072A04D4E401E54A0139E2 | tee rsa-pub--template
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCmWvDj+YjIdhMOGPw1Y0zWiQoxO2LkzZs4anzy4Rb1m2l3+rRRfyf/AnEBWoy5+NeW/xSqK5QDN9y4AODzwxEXFV2os3bnPzLz2qv2srSm+OPPRBVcGCiLxin9Mj+xEzgzLnF39hwTRHQC51gCOdz9g3O4Srfe2x6ImHBniMeqTiN+iQ/QfTcyCI2/pIf/EHV3v5uhN1oZlzW68w0nYG9JdkWjtUwWlw92h8NeFOEVJx7MOFJ3S4QEiouFtTDv8d546gfdsLIXS1QkJCebpG1BaWh8IvTfply3y8xAHFR8TU5XKWVbRHKHsOK2pvGtRzx8huOL0rrYiQQmvj9FVcvH openpgp:0x4A0139E2
```

Now you have to allow GPG' ssh agent to use this key:
```
$ gpg --list-keys --with-keygrip /home/kang/envs/dsmctools-team/gnupg/pubring.kbx
------------------------------------------------
pub   rsa2048 2020-03-02 [SC]
      5BC14997737EA46A508DEA5F08B6C27B68FB5C3E
      Keygrip = 086E27D66A7170CAEE157988F046DC1604EDF09D
uid           [ultimate] Insert Your Name <your@mail.tld>
sub   rsa2048 2020-03-02 [E]
      Keygrip = 41307223DA493562124158F930BDA82A42736967
sub   rsa2048 2020-03-02 [A]
      Keygrip = 02ECB408B80E268268278B937BC194D8EC64FF54

$ echo \# rsa-pub--template >> gnupg/sshcontrol # To have all keys tagged on sshcontrol file
$ echo 02ECB408B80E268268278B937BC194D8EC64FF54 >> gnupg/sshcontrol

$ cat sshconfig-\*
Host github--template
  UserKnownHostsFile ~/envs/template/known-hosts
  Hostname github.com
  IdentityFile ~/envs/template/rsa-pub--template
  User git
```

When you activate your environment, a gpg with a ssh agent emulator will read the comment 
`openpgp:0x4A0139E2` and will use your private key asking you for a password.

### gopass

Is is used for store team passwords securely.


For the gopass agent to be activated `GOPASS_CONFIG` environment variable and a file in
that path sould exist.

> NOTE: if you use the keys subdirectory gopass will fail with strange errors of an
> unexisting repo (because could not find de `.git` folder) as it does not use git
> CLI for testing if the folder is a repo on a higher folder on the tree.
>
> If this happens, `autosync` has to be disabled from `gopass` and `path` in `config.yaml`
> should be changed to something like `path: gpgcli-noop-fs+file://`.

