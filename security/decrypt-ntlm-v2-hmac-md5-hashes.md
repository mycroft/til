# Decrypt ntlm v2 hmac-md5 hashes with hashcat

On a recent challenge, a `pcapng` file containing a ntlm2 authentication exchange was given and it was asked to find out the password.

A hmac-md5 ntlm v2 auth is based on a server challenge, with which the client will compute a `NTProofStr` with the request data (ntlmv2 response without the ntproofstr).

`wireshark` decodes everything quickly, just find out the two packets:

- STATUS_MORE_PROCESSING_REQUIRED, NTLMSSP_CHALLENGE that contains `NTLM Server Challenge`;
- NTLMSSP_AUTH, User: <domain>\user that contains the NTLMv2 response with the `NTProofStr` and the authentication data. 


## Running hashcat

An hashfile composed of the username, domain, server challenge, ntlm proof and data is required to try to crack the user password.

File's format is:

```
username::domain:server challenge:ntproofstr:data
```

Hopefully, it will work as it and return the found password:

```sh
$ hashcat hashes.txt /opt/sec/SecLists/Passwords/xato-net-10-million-passwords-1000000.txt
hashcat (v6.2.6) starting in autodetect mode

...

Dictionary cache built:
* Filename..: /opt/sec/SecLists/Passwords/xato-net-10-million-passwords-1000000.txt
* Passwords.: 1000000
* Bytes.....: 8557632

JOHN.DOE::blah.local:<challenge>:<data>:<found password>
```


## References

- https://infosecwriteups.com/cracking-hashes-with-hashcat-2b21c01c18ec
- https://hashcat.net/wiki/doku.php?id=example_hashes
- https://www.vaadata.com/blog/fr/authentification-ntlm-principes-fonctionnement-et-attaques-ntlm-relay/