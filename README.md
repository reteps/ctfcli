# ctfcli

*ctfcli is a tool to manage Capture The Flag events and challenges.*

`ctfcli` provides challenge specifications and templates to make it easier to generate challenges of different categories. It also provides an integration with the [CTFd](https://github.com/CTFd/CTFd/) REST API to allow for command line uploading of challenges and integration with CI/CD build systems.

`ctfcli` features tab completion, a REPL interface (thanks to [Python-Fire](https://github.com/google/python-fire)) and plugin support for custom commands.

*WIP: ctfcli is an alpha project and changes will happen. Be sure to pin versions and read the CHANGELOG when updating.*

# Installation and Usage

ctfcli can be installed with `pip install ctfcli`

## 1. Create an Event

ctfcli turns the current folder into a CTF event git repo. It asks for the base url of the CTFd instance you're working with and an access token.

```
❯ ctf init
Please enter CTFd instance URL: https://demo.ctfd.io
Please enter CTFd Admin Access Token: d41d8cd98f00b204e9800998ecf8427e
Do you want to continue with https://demo.ctfd.io and d41d8cd98f00b204e9800998ecf8427e [y/N]: y
Initialized empty Git repository in /Users/user/Downloads/event/.git/
```

This will create the `.ctf` folder with the `config` file that will specify the URL, access token, and keep a record of all the challenges dedicated for this event.

## 2. Add challenges

Events are made up of challenges. Challenges can be made from a subdirectory or pulled from another repository. Remote challenges are pulled into the event repo and a reference is kept in the `.ctf/config` file.

```
❯ ctf challenge add [REPO | FOLDER]
```

```
❯ ctf challenge add crypto/stuff
```

```
❯ ctf challenge add https://github.com/challenge.git
challenge
Cloning into 'challenge'...
remote: Enumerating objects: 624, done.
remote: Counting objects: 100% (624/624), done.
remote: Compressing objects: 100% (540/540), done.
remote: Total 624 (delta 109), reused 335 (delta 45), pack-reused 0
Receiving objects: 100% (624/624), 6.49 MiB | 21.31 MiB/s, done.
Resolving deltas: 100% (109/109), done.
```

## 3. Install challenges

Installing a challenge will automatically create the challenge in your CTFd instance using the API.

```
❯ ctf challenge install [challenge.yml | DIRECTORY]
```

```
❯ ctf challenge install buffer_overflow
Found buffer_overflow/challenge.yml
Loaded buffer_overflow
Installing buffer_overflow
Success!
```

## 4. Update challenges

Syncing a challenge will automatically update the challenge in your CTFd instance using the API. Any changes made in the `challenge.yml` file will be reflected in your instance.

```
❯ ctf challenge sync [challenge.yml | DIRECTORY]
```

```
❯ ctf challenge sync buffer_overflow
Found buffer_overflow/challenge.yml
Loaded buffer_overflow
Syncing buffer_overflow
Success!
```

The additional flag, `--ctfd-name [NAME]` can be passed to optionally sync to a challenge with a different-name in CTFd.

## 5. Verify & pull challenges

If, at any point, changes may have been made to a challenge through the CTFd UI by an admin. To verify that your challenge.yml file matches remote contents, you can use:

```
❯ ctf challenge verify [challenge.yml | DIRECTORY] [--verify-files] [--verify-defaults]
```

If the `--verify-files` flag is set, challenge files will be downloaded and the binary files will be compared.

If the `--verify-defaults` flag is set, challenge files will be verified to make sure they include default optional keys present on CTFd.
If you want to pull down the latest version of the challenge, and its challenge files, you can use:

```
❯ ctf challenge verify [challenge.yml | DIRECTORY] [--update_files] [--create-files] [--create_defaults]
```

If the `--update_files` flag is set, the latest version of every file will be redownloaded from CTFd.

If the `--create-files` flag is set, any new files added to through the CTFd UI will be downloaded to the same directory as the `challenge.yml` file.

If the `--create-defaults` flag is set, any optional default values will be added to the `challenge.yml`.

**This is a destructive action! It will overwrite the local version of `challenge.yml` with the version on CTFd!**
# Challenge Templates

`ctfcli` contains pre-made challenge templates to make it faster to create CTF challenges with safe defaults.

```
ctf challenge new
                ├── binary
                ├── crypto
                ├── programming
                └── web
```

```
❯ ctf challenge new binary
/Users/user/.virtualenvs/ctfcli/lib/python3.7/site-packages/ctfcli-0.0.1-py3.7.egg/ctfcli/templates/binary/default
name [Hello]: buffer_overflow

❯ ls -1 buffer_overflow
Makefile
README.md
WRITEUP.md
challenge.yml
dist/
src/
```

**Contributions welcome on improving the challenge templates to make CTF challenges better for everyone!**

# Challenge Specification

`ctfcli` provides a [challenge specification](ctfcli/spec/challenge-example.yml) (`challenge.yml`) that outlines the major details of a challenge.

Every challenge generated by or processed by `ctfcli` should have a `challenge.yml` file.

The specification format has already been tested and used with CTFd in production events but comments, suggestions, and PRs are welcome on the format of `challenge.yml`.

# Plugins

`ctfcli` plugins are essentially additions to to the command line interface via dynamic class modifications. See the [plugin documentation page](docs/plugins.md) for a simple example.

*`ctfcli` is an alpha project! The plugin interface is likely to change!*
