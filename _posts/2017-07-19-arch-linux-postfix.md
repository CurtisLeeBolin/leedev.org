---
layout: post
title: 'Arch Linux: Postfix configured for Relay Only with TLS, SASL, and simple Virtual
  Alias'
date: '2017-07-19 19:23:23'
---

Install `postfix` and `cyrus-sasl`:
```
# pacman -Syu postfix cyrus-sasl
```

Wipe a few files:
```
# rm /etc/postfix/{main.cf,master.cf,virtual,aliases}
# touch /etc/postfix/{main.cf,master.cf,virtual,aliases}
```

Edit `/etc/postfix/main.cf`:
```
smtp_tls_security_level = may
myhostname = leedev.org

# relay only 
mydestination =

# local incoming connections only
#inet_interfaces = 127.0.0.1

# To stop annoying log messages:
alias_maps = hash:/etc/postfix/aliases
compatibility_level = 2

smtp_tls_note_starttls_offer = yes
smtpd_tls_security_level = may
smtpd_tls_received_header = yes
smtpd_tls_cert_file = /etc/letsencrypt/live/leedev.org/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/leedev.org/privkey.pem
```

Edit `/etc/postfix/master.cf`:

```
#
# Postfix master process configuration file.  For details on the format
# of the file, see the master(5) manual page (command: "man 5 master" or
# on-line: http://www.postfix.org/master.5.html).
#
# Do not forget to execute "postfix reload" after editing this file.
#
# ==========================================================================
# service type  private unpriv  chroot  wakeup  maxproc command + args
#               (yes)   (yes)   (no)    (never) (100)
# ==========================================================================
smtp      inet  n       -       n       -       -       smtpd
smtpd     pass  -       -       n       -       -       smtpd
submission inet n       -       n       -       -       smtpd -v
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_reject_unlisted_recipient=no
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
  -o milter_macro_daemon_name=ORIGINATING
pickup    unix  n       -       n       60      1       pickup
cleanup   unix  n       -       n       -       0       cleanup
qmgr      unix  n       -       n       300     1       qmgr
tlsmgr    unix  -       -       n       1000?   1       tlsmgr
rewrite   unix  -       -       n       -       -       trivial-rewrite
bounce    unix  -       -       n       -       0       bounce
defer     unix  -       -       n       -       0       bounce
trace     unix  -       -       n       -       0       bounce
verify    unix  -       -       n       -       1       verify
flush     unix  n       -       n       1000?   0       flush
proxymap  unix  -       -       n       -       -       proxymap
proxywrite unix -       -       n       -       1       proxymap
smtp      unix  -       -       n       -       -       smtp
relay     unix  -       -       n       -       -       smtp
showq     unix  n       -       n       -       -       showq
error     unix  -       -       n       -       -       error
retry     unix  -       -       n       -       -       error
discard   unix  -       -       n       -       -       discard
local     unix  -       n       n       -       -       local
virtual   unix  -       n       n       -       -       virtual
lmtp      unix  -       -       n       -       -       lmtp
anvil     unix  -       -       n       -       1       anvil
scache    unix  -       -       n       -       1       scache
```

Edit `/etc/postfix/virtual`:
`wanted email` `local user`

```
no-reply@leedev.org blah-app
```

Generate the alias databases:
```
# postmap /etc/postfix/virtual
# postalias /etc/postfix/aliases
```

*Regenerate the database and reload postfix anytime you change to the config file.*

Edit `/etc/sasl2/smtpd.conf`:

```
pwcheck_method: saslauthd
mech_list: PLAIN LOGIN
log_level: 7
```

Edit `/etc/pam.d/smtp`:
*Requried since pambase 20190105.1-1 .*

```
#%PAM-1.0
auth            required        pam_unix.so
account         required        pam_unix.so
```

Enable and start `postfix` and `saslauthd.service`:
```
# systemctl enable --now postfix.service saslauthd.service
```