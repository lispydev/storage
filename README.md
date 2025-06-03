# Storage

A pass-like encrypted and remotely synchronized file storage

## Install

![Download the storage script](https://raw.githubusercontent.com/lispydev/storage/refs/heads/master/storage), move it to `/usr/local/bin/storage` and mark it as executable with `chmod +x /usr/local/bin/storage`

You will need Python installed to run `storage`.


## Basic useage

`storage` uses GPG to encrypt stored files. To define the GPG secret key to use, run:
```
gpg --gen-key  # create a new GPG key
storage key mykey@gmail.com  # save the GPG key for this email address
storage key  # display the GPG key fingerprint
```

When creating a GPG key, you should always use a passphrase to prevent someone with physical access to any of your machines from accessing your private files.


## Storing and retrieving files


Files are stored in a git repository at `~/.file-store`.


The following will save `bill.pdf` as `bills/<current-date>.pdf.gpg` under the storage file store.
```
storage save bill.pdf bills/$(date -I).pdf
```

To retrieve the file:
```
storage get bills/2024-11-18.pdf bill.pdf
```

If you do not need it anymore:
```
storage rm bills  # rm -r ~/.file-store/bills(.gpg)
```

If you delete important files, you can still recover them with git:
```
# check the current branch
storage git branch
storage git log
# go back to the previous commit
storage git checkout 1e570353ecac0768ff92533f4f298870f7de0881
storage get bills
# go back to the main branch
storage git checkout master
```



## Server synchronization with Git and SSH

You can configure a remote server with SSH access to synchronize your files before any accident:
```
# file-store must be a valid git repository
storage git remote add origin user@server:file-store
storage git remote -v # display the configured server(s)
```

To create a git repository on your server:
```
ssh user@server
git init --bare file-store
```

To synchronize:
```
storage git pull  # fetch updates before commiting new things
```
```
storage git push  # push new updates to the server
```


If you want to have more backups, you can configure a mirror repository on an other server. However, using public servers like Github is dangerous for your privacy if your documents must stay confidential.


## Export encrypted files and GPG keys

In order to synchronize a new machine with your `storage` server, you need to:
- install gpg, ssh and storage
- configure ssh keys for prompt-less connections
- clone the repository into `~/.file-store` with `git clone user@server:file-store ~/.file-store`
- import the GPG public and secret keys on the new machine

To export your GPG keys:
```
gpg --export --armor <myemail> > myemail.gpg
gpg --export-secret-keys --armor <myemail> > myemail_secret.gpg
```


To import on the new machine:
```
gpg --import myemail.gpg
gpg --import myemail_secret.gpg
```
