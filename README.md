# Secrets
A repo with encrypted secrets

## Getting Started

1. [Install SOPS](https://github.com/mozilla/sops)
2. Generate PGP key
3. Add environment variable for your key id
4. Make new file
5. Verify encryption

## Details

[Installing SOPS](https://github.com/mozilla/sops) should be straight forward if you are on a macOS or Linux computer. Untested on Windows and all mobile phone operating systems.

Generating a PGP key is also straight forward. After installing SOPS, try this `gpg --full-generate-key`.

Adding the env var goes like this

```
gpg --list-keys
export SOPS_PGP_FP=your key id
export GPG_TTY=$(tty)
gpgconf --reload gpg-agent
```

Add the two `export ...` lines to `~/.bashrc` so it persists across shells.

Making a new encrypted file is easy. Type `sops hello-world.yaml` and it will open a file in your editor defined by the `$EDITOR` env var. When you quit the editor, the contents will be encrypted.

Verify the file was encrypted by viewing the contents with `cat hello-world.yaml`. The values should all be encrypted and there should be some additional metadata about the encryption details.

## Adding many users to access a file

To add a new user to a file, you first need the public key for that user. They may transmit this information to you on an insecure channel (that's why it is a public key) and generate it with `gpg --export --armor <your key id>`.

Transmit this information to any person who has the ability to decrypt the file you wish to view.

This person will use this information to add a new key to a file.

1. Import the public key
2. Add the public key as a valid decrypt key

After saving the public key to a file, import it with the following `gpg --import <path to public key file>`

Inform sops to add the public key to the valid decrypt list like so
`sops -r -i --add-pgp <your key id> hello-world.yaml`

Commit the change to the repo and push those changes. Use your fav git front end or type these things.

```
git add hello-world.yaml
git commit
git push origin master
```

## Delegating Access

Noisebridge is a collective, so there is no single apointed admin. This system requires a single person to kick off access but provides a system where all with access have the ability to give new people the same access.

## Known GPG errors

This system is not compatible with gpg version 1. You must use gpg 2 with the `gpg-agent` program. Depending on your platform, this may require some manual intervention, which is documented [in an issue filed on the sops project](https://github.com/mozilla/sops/issues/304#issuecomment-381727428).

## Alternative key methods

Sops also supports Amazon KMS. This removes the complexity of PGP and adds a monetary cost to maintain key material in your personal AWS account. Documentation for KMS will be added to this repo by the first person who needs that type of access.