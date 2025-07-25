# Big Bang Developers

A default GPG key has been configured here to enable local development and testing. You will need access to the AWS SIL kms to decrypt `dev-gpg.asc.enc`, then import that key into your local keyring using `gpg --import`

Ensure your AWS cli has a current login session and run:
```sh
sops --decrypt dev-gpg.asc.enc | gpg --import
```
