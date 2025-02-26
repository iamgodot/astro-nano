---
title: Py-tldr
description: A Python CLI client for tldr pages
date: 2023-06-15 02:19
draft: false
repoURL: https://github.com/iamgodot/py-tldr
---
Py-tldr is a Python CLI client for [tldr pages](https://github.com/tldr-pages/tldr).

![](/py-tldr.gif)

## Installation

```bash
pip install py-tldr
```

## Usage

```
$ tldr --help

Usage: tldr [OPTIONS] [COMMAND]...

  Collaborative cheatsheets for console commands.

  For subcommands such as `git commit`, just keep as it is:

      tldr git commit

Options:
  -v, --version                   Show version info and exit.
  --edit-config                   Open config file with an editor.
  -p, --platform [android|common|linux|osx|sunos|windows]
                                  Override current operating system.
  -L, --language TEXT             Specify language of the page(with no
                                  fallbacks), e.g. `en`.
  -u, --update                    Update local cache with all pages.
  -h, --help                      Show this message and exit.
```

Config file is usually located as `~/.config/tldr/config.toml`, you can use `--edit-config` to create a default one, which will contain the following content:

```toml
page_source = "https://raw.githubusercontent.com/tldr-pages/tldr/main/pages"
language = "en"
platform = 'linux'
proxy_url = ""

[cache]
enabled = true
timeout = 24
download_url = "https://tldr.sh/assets/tldr.zip"
```

Cache is enabled implicitly, with 24 hours as expiration time by default.

A proxy url can be set for convenience, proxy envs such as `HTTP_PROXY` will also work.