---
title: Create-python-app
description: A Python package template built with Cookiecutter
date: 2023-09-03 02:29
draft: false
repoURL: https://github.com/iamgodot/create-python-app
---
Create-python-app is a Python package template based on [cookiecutter](https://github.com/audreyr/cookiecutter).

## Features

- Integrate [pdm](https://github.com/pdm-project/pdm) for dependency management.
- Proper github workflows setup.
- Optional cli setup with click.
- Delicate Makefile.

## Usage

```bash
# Install cookiecutter
pipx install cookiecutter

# Generate your project, just follow the prompts and fill out
cookiecutter https://github.com/iamgodot/create-python-app

# Let's say your project slug is greeting
cd greeting

# Make sure you have pdm installed
make install
make test
```

To use the `release` workflow, you have to add `PYPI_API_TOKEN` in repo secrets.