# 2fa Command Line Authentication

If you have 2fa ([2 factor authentication](https://help.github.com/articles/securing-your-account-with-two-factor-authentication-2fa/)) enabled on GitHub you can't use your password to authenticate at the command line on Linux.

After a while I found a solution that suits for me:

## Steps

**1.** Create an **access token** for command-line use. See: https://help.github.com/articles/creating-an-access-token-for-command-line-use/

**2.** Create the `~/.netrc` file. It should look like:

```
machine github.com
login username
password YOUR_TOKEN
protocol https

machine gist.github.com
login username
password YOUR_TOKEN
protocol https
```
with `YOUR_TOKEN` created in the previous step.

More about `.netrc` file: `man 5 netrc`

**3.** Encrypt `.netrc` file.

```shell
# Generate key
gpg --gen-key
# Encrypt file with this key
gpg -r <user-id-key> -e .netrc
# Give appropiate permissions
chmod 400 ~/.netrc.gpg
# Remove original file
rm ~/.netrc
```

This generates a `.netrc.gpg` file.

**4.** Make available the git credential helper:

```shell
# Put the script somewhere in PATH
sudo cp /usr/share/doc/git/contrib/credential/netrc/git-credential-netrc /usr/local/bin/
# Mark it as executable
sudo chmod +x /usr/local/bin/git-credential-netrc
# Set Git configuration value
git config --global credential.helper netrc
```

**NOTE**: You can set two git credential helpers. This way you can combine with `git-credential-cache` and temporarily store authentication in memory. You have to declare the helpers in an specific order and your `~/.gitconfig` file should look like this:

```
[credential]
  helper = cache --timeout 7200
  helper = netrc
```

### More sources

- https://git-scm.com/book/es/v2/Git-Tools-Credential-Storage
- https://git-scm.com/docs/git-credential-cache

## Future work: Custom Credential Tool

It could be interesting to develop a credential helper.

## Contribution

PRs and reporting issues are welcome.
