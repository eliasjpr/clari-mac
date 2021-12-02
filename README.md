# clari-mac

Ascript to set up a macOS laptop for development.

- It can be run multiple times on the same machine safely.
- It installs, upgrades, or skips packages based on what is already installed on the machine.

[Clari Onboarding Docs](https://docs.google.com/document/d/1cVKqh6ouGiacjR_bIpaR8IFIn4ysw8Xnrldl2y_Trlo/edit#)

Install
-------

Download the script:

```sh
curl --remote-name https://raw.githubusercontent.com/eliasjpr/clari-mac/main/mac-setup
```

Review the script (avoid running scripts you haven't read!):

```sh
less macç
```

Execute the downloaded script:

```sh
sh mac√ 2>&1 | tee ~/laptop.log
```

Optionally, review the log:

```sh
less ~/laptop.log
```

Debugging
---------

Your last Laptop run will be saved to `~/laptop.log`.
Read through it to see if you can debug the issue yourself.
If not, copy the lines where the script failed into a
[new GitHub Issue](https://github.com/eliasjpr/clari-mac/issues/new) for us.
Or, attach the whole log file as an attachment.

Customize in `~/.laptop.local`
------------------------------

Your `~/.laptop.local` is run at the end of the Laptop script.
Put your customizations there.

For example:

```sh
#!/bin/sh

brew bundle --file=- <<EOF
brew "Caskroom/cask/dockertoolbox"
brew "go"
brew "ngrok"
brew "watch"
EOF

default_docker_machine() {
  docker-machine ls | grep -Fq "default"
}

if ! default_docker_machine; then
  docker-machine create --driver virtualbox default
fi

default_docker_machine_running() {
  default_docker_machine | grep -Fq "Running"
}

if ! default_docker_machine_running; then
  docker-machine start default
fi

fancy_echo "Cleaning up old Homebrew formulae ..."
brew cleanup
brew cask cleanup

if [ -r "$HOME/.rcrc" ]; then
  fancy_echo "Updating dotfiles ..."
  rcup
fi
```

Write your customizations such that they can be run safely more than once.
See the `mac` script for examples.

Laptop functions such as `fancy_echo` and
`gem_install_or_update`
can be used in your `~/.laptop.local`.

