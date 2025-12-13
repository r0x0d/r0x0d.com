---
layout: post
title: 'Packaging for Fedora: General purpose cookiecutter template'
tags:
- packaging
- template
- cookiecutter
- fedora
categories:
  - Packaging
  - Fedora
image:
  path: assets/imgs/cookiecutter.jpg
  alt: Cookies cut in format of a person
date: 2025-12-12 14:38 -0300
---
To make the effort on packaging easier, I developed a custom
[cookiecutter](https://github.com/cookiecutter/cookiecutter?tab=readme-ov-file)
template to help bootstraping this work.

<!-- TODO(r0x0d): Update this post with the URLs for python/rust packaging -->
The template currently comes with pre-defined settings for packaging `Rust` and
`Python` applications, which is my main target as of now. The details on how to
package such proejcts will be detailed in another blog post, not here. In here,
we will try to focus on how this cookiecutter template works and how you can
use it in your workflow (if you like it.)

## Getting started

In order to use this template, you will need to have `cookiecutter` and
`jinja2_time` installed (preferably in a virtualenv), so let's begin with:

```bash
pip install cookiecutter jinja2_time
```

The `jinja2_time` is used here as a convenience to update the current year in
the `LICENSE` file once it is generated.

Once both are installed, we can proceed with calling cookiecutter to generate
our new project:

```bash
# This will pull the template with ssh
cookiecutter git@github.com:r0x0d/cookiecutter-fp
```

You will be prompted to answer a few bootstraping questions for the template.
Don't worry, this won't execute any hidden hooks on your system. This is mainly
to track the correct names of the upstream project, GitHub/Gitlab/Codeberg URLs
and etc. You should have something looking like this:

```bash
  [1/6] project_slug (rust-my-awesome): my-super-project
  [2/6] pkg_name (my-awesome): super-project
  [3/6] upstream_repo (https://github.com/my/awesome-repo): https://github.com/my-username/super-project
  [4/6] Select tool_2_rpm
    1 - rust2rpm
    2 - pyp2spec
    Choose from [1/2] (1): 2
  [5/6] year (2025):
  [6/6] copr_username (<your username>): my-username
```

Let me break down what each prompt above means for you:

| Prompt        | Description                                                                             |
| ------------- | --------------------------------------------------------------------------------------- |
| project_slug  | Used to match the downstream package name (e.g; `python-flask`, `rust-serde`, etc...)   |
| pkg_name      | The name of the package you are trying to get to fedora (e.g; `flask`, `serde`, etc...) |
| upstream_repo | URL to the upstream repository for the package (e.g; `Github`, `Gitlab`, etc...)        |
| tool_2_rpm    | Choose from `rust2rpm` or `pyp2spec` (more details on that below)                       |
| year          | The year used in the `LICENSE` file for your mid-stream repository                      |
| copr_usernam  | The [COPR](https://copr.fedorainfracloud.org) username to manage the builds             |

Once all of the prompts are answered, you will se a new folder in the directory
you are that matches the `project_slug` name you entered before.

> It is not required to set the `project_slug` to be the same downstream
> package name, but, this help in organization and knowing that you will have
> the same data when you go to the package review process, avoiding confusion
> and unnecessary drawback.
{: .prompt-info }

## What to do with a new project?

Once you have created a new project with the above `cookiecutter` command, you
are now ready to start modifying, building and eventually, submit it to package
review. This part is one of the most important in the process of packaging, as
it is here where you will do your experimentation and get the builds right, so
don't be afraid of messing stuff up 😊.

This template comes with a set of `make` targets that will help you quickly
experiment with the builds and iterate over the changes. We will talk about
them in each of the sub-sections bellow.

### Creating a new copr repository

First things first, we need to create a new copr repository for you to track
your builds. In Fedora, you can use `mockbuilds` that will execute locally, but
they will, at some point, get your directories dirty. I'm not sure about you,
but I don't like having dangling files all around my computer that I won't
remember why they are there 6 months or 1 year later.

#### Setting up a copr account

You will need to create a new [Fedora account
(FAS)](https://accounts.fedoraproject.org/) first before working with copr,
this will be necessary for further steps as well when we reach the package
review process.

Once you have your new FAS account, go back to
[copr](https://copr.fedorainfracloud.org/coprs/) and click on the `OIDC login`
to use your FAS credentials.

With those steps done, go ahed to [copr
api](https://copr.fedorainfracloud.org/api/) page to grab your credentials.
This is very important as we can't create a repository for you in the next
steps if you don't get your API keys set up.

> Don't worry, that page tells you exactly where to place the contents you
> grabbed from the site.

#### Installing necessary rpms

There is a set of RPMs that are necessary for you to install on your system in
order to proceed, we can start with the following:

* fedpkg
* copr

```bash
sudo dnf instal fedpkg copr
```

Alternatively, if you don't like installing things directly to your system, or
if you are like me who is using Fedora Silverblue, then I got you covered... I
have created a toolbox environment for Fedora packaging that comes with most of
what you will need configured.

To use my fedora-packaging contianer, you could do it with `toolbox`/`distrobox`:
```bash
toolbox create --image quay.io/toolbox-dev/environment/fedora-packaging fedora-packaging
```

Below you can see the contents of that container:

{% github_code r0x0d/toolbox-dev/blob/main/toolbox/environment/fedora-packaging.Containerfile %}

Once everything is ready and set up, we can now proceed to create the
repository with the `make` target:

```bash
make create-copr-repository
```

### Generating the specfile

In order to generate the specfiles for the given project you want to package,
it is highly recommended that either `rust2rpm` or `pyp2spec` is used,
depending on the type of project you are trying to package.

* rust2rpm (required if you're packaging rust applications)
* pyp2spec (required if you're packaging python applications)

```bash
sudo dnf install rust2rpm pyp2spec
```

Once the tool is installed, it is time to run the below make target to generate
the specfile automatically:

```bash
make spec
```

Some manual changes will be necessary in case you are packaging `python`
applications due to the limitations of the `pyp2spec` tooling. For `rust` RPMs,
it should be straightforward.

Depending on the project you will package, some patching will be required in
order to have a successful build. The specifics on how to patch will be covered
on a different post.

### Building the project

Now that we already have the specfile in place, it is time to trigger our first
build! First, we will need to download the sources from the specfile, given
that we don't have a dist-git set up yet, we will reloy on `srpm` generation,
which requires that you have the tarball sources present in the same directory
as of your specfile. Luckily, the template also comes with two useful targets
that can be used to bootstrap and automate this process.

```bash
# Get the sources from the specfile
make sources

# Submit a new build to copr
make build
```

Once the `build` is triggered, and if everything is working correctly, you will
see that your `srpm` will be uploaded to copr and a build will start to happen!

#### Watching the logs with fuzzytail

The cookiecutter template comes by default with a target for watching the logs
of your copr build:

```bash
make logs
```

To know more about `fuzzytail`, check out my other blog post [Packaging for
Fedora: fuzzytail! Watching logs on your
terminal](https://r0x0d.com/posts/packaging-for-fedora-fuzzytail-watching-logs-on-terminal/).

### Tracking the repository in github (optional)

Lastly, as an optional task, you can create a github repository to track your
work (either because you want to version it, or you are working on mulptiple
machines). This step is not required at all, so it may be safely ignored if you
have enough experience on this process.

There is a set of RPMs that are necessary for you to install on your system in
order to proceed, we can start with the following:

* gh-cli

```bash
make crate-gh-repository
```

## Contributing

If you would like to contribute to the cookiecutter template, please, feel free
to do so in the below repository:

{% github r0x0d/cookiecutter-fp %}
