# Moving mails from one mailbox to another with formail

Use case: I've been using a specific folder for mails I'm considering to be spam, and mails in this folder is sent everyday to spamassassin's `sa-learn --spam` for adding tokens to the bayes database.

I found out `formail` (available in the `procmail` package) will take an inbox as input, and will reformat mails, or allow to split/reformat/append mails before adding the mails to target mailbox.

Eg, the following command will show the 2nd mail of the mailbox:

```sh
$ cat ~/mail/inbox | formail +1 -1 -s
```

This command will print all first lines of each mail in the box, excepting the first one:

```sh
$ formail +1 -s head -n1 < /var/mail/$USER
```

Whole script, as a sample, to move all mails from `Junk` to `Trash`, sending then to `sa-learn` in between:

```sh
#!/bin/sh

JUNKBOX=/home/$USER/mail/Junk
TRASHBOX=/home/$USER/mail/Trash

/usr/bin/sa-learn --spam --mbox < ${JUNKBOX}
formail < ${JUNKBOX} >> ${TRASHBOX}
rm -f ${JUNKBOX}
touch ${JUNKBOX}
```
