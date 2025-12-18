---
layout: post
title: 'Packaging for Fedora: Python projects'
categories:
- Fedora
- Packaging
tags:
- packaging
- python
- fedora
image:
  path: assets/imgs/python-box.jpg
  alt: A python inside a acrylic box
date: 2025-12-18 09:49 -0300
---
In this post, I want to share with you what I know about packaging `Python`
projects for Fedora. I must start this text admitting that I don't know a whole
lot (_yet_), but I figured that it would be very valuable, not for myself, but
also for others, if I shared my learning process and the roadblocks I have been
facing recently. More like documenting what is happening and how I solved such
problems.

## How do I get started?

Packaging can be seem as a challenging task, no matter your level of
experience, but, let's try to understand how it works from the beginning.

Before jumping straight to writing specfiles, patches and all the fun stuff,
let's first understand a couple of resources that can make your life easier in
this process. Fedora has a great online documentation available to everyone
that can guide you in this process, going from what you need to know about
packaging in general, and down to the more specific topics in each language,
for instance, take a look at the below docs:

{% post_card https://docs.fedoraproject.org/en-US/packaging-guidelines/ hide_image:true %}

The `Fedora Packaging Guidelines` Has a complete set of information about
packaging and what you should and should not do when packaging any software.

That piece of documentation goes in detail about all the pieces of packaging,
from licensing to architecture support, build scripts and much, much more! It's
always good to read or bookmark that URL for further reference when needed.

Now, if you take a look at the below one:

{% post_card https://docs.fedoraproject.org/en-US/packaging-guidelines/Python/ hide_image:true %}

The `Python Packaging Guidelines`, much like the `Fedora Packaging Guidelines`
will give you a complete tour on the specifics about packaging `Python`
applications, whici, is what we are intetrested in here.

In that one, you will learn about the structurue that is expected of your
specfile, where to pull the source tarball, and many more information.

> Keep in mind that, while it is always *recommended* that you read those
> documentation above, you can always bookmark and check them before submiting
> anything for review. Depending on your style of work, it may be more
> interesting to read such docs on the fly.
{: .prompt-info }

Knowing that such materials exists will be extremely helpful to us when we
proceed to submit our newly built package for review in
[bugzilla](https://bugzila.redhat.com), but don't worry, we will cover this in
another post, as there is quite a lot of information for us to go through. This
post is more intended to be a hands-on, practical, python packaging and not
really about package reviews or procedures.

### Finding your upstream community/project

Finding a community that you like is one of the most crucial steps before you
start hacking your packages. This step is important because if you find
yourself trying to package something that is not even related to your
day-to-day work or of your interest, you may find it boring when errors and
workarounds appears. My advice to you, my fellow beginner packager, is:

* Pick something that you use on your workflow and that does not exist in
Fedora currently.

I'm not saying that packaging just for the fun of it is wrong, but, when you
give a purpose to your work, then, it does not feel boring or a "hard
requirement". Our goal here is that you can learn how packaging is done, and
also, have fun 😃!

Once you know what you want to introduce to Fedora, either because you need it,
or you have a higher purpose (like contributing with a given community), then
you are all set to go!

## What is a specfile?

Working with specfiles can be challenging with many terms to identify and
understand, but once you get the hang of it, the packaging part starts to be
fun! I could go over here in details of each field, component and techniques
for writing good specfiles, but, that would make this post be three times
bigger than it already is. Instead, since this topic is huge and contain a lot
of information, I will leave here the official documentation of the RPM
Packaging Guide linked directly to the explanation of what is a specfiles, and
instead of deep diving on it myself, let's try to get to other topics that are
not covered in the official docs.

{% post_card https://rpm-packaging-guide.github.io/#what-is-a-spec-file %}

### Pulling sources from pypi or GitHub/Gitlab/etc?

When working with packaging, we need to identify from where we want to pull the
sources for our builds. The Python Packaging Guidelines states that you *MAY*
pull the sources from PyPi for better convenience, however, that scenario may
be complicated some times.

Let's see below when to pull from PyPi or when to pull from another SCM.

#### When to pull from PyPI?

Usually, I like to start my packaging process by pulling the sources from PyPi
and taking a good look at what is being present in the source tarball. You are
not required to do this, but I prefer starting my workflow like that.

For instance, let's take a look at this section of one of my specfile:

{% github_code https://github.com/r0x0d/python-sse-starlette/blob/c7a72897695d492b1c4cdda64b30049b3ddbde00/python-sse-starlette.spec#L1-L8 %}

You see that I'm using this macro called `%pypi_source`, right? This is a
[Convenience
Macro](https://docs.fedoraproject.org/en-US/packaging-guidelines/Python/#_convenience_macros)
that we can use to generate the full PyPi URL. With that, you only need to pass
the package name in PyPi and optionally, the version, as it pick the version
from the `Version` field.

The good part is that it is convenient to pull the sources from PyPi directly
with this macro, but, sometimes the upstream maintainer may not include
required files/folders that we need to use in downstream packaging. For
instance, the upstream project may be using a new shiny tooling/builder that
generate metadata differently what is currently expected (like poetry, uv,
setuptools, etc...). This can cause problem during the package build as you may
be missing the license information, tests folders (which is good to execute
during the specfile builds) and more.

When that happens, we may need to switch to pull sources from other SCM
variants, like GitHub. From there, it's more likely to get a full overview of
the project setup, and then you can manually pick what you want from the
tarball.

#### When to pull from GitHub/Gitlab/etc?

As stated before, sometimes the PyPi sources may not contain all the required
metadata, tests, or essential files for your package building, when such cases
happen, we need to switch our plan and start pulling from the upstream SCM, for
instance, take a look at this other package:

{% github_code https://github.com/done-packaging/python-openapi-pydantic/blob/main/python-openapi-pydantic.spec#L1-L8 %}

This project was exceptional one, as the PyPI sources didn't contain the tests
folder for me to check under the `%check` directive. For that, I had to change
my approach to pull from GitHub (were the project is hosted), as in that one, I
would have the same copy of the project, but with the tests folder that I
needed.

You can go with the upstream SCM directly if you wish, that's not a big deal at
all. This is more a question of preference and from where you want to pull your
trusted tarball.

### Patching in downstream

Patching is one of the most needed useful techniques that we can bring to the
packaging world! This help us modify the behavor of the project before the
builds. Why this is necessary, you may think? Well, let's think that your
project depends on `libXX == 1.0.0`, but Fedora only has `libXX == 0.9.0`
packaged in the repositories, what should you do now?

Well, it's always possible to ask for the maintainer of the `libXX` package to
update to the newest version, but, that may not be possible all the time.
Instead, it's when patching comes to the rescue!

You could use different strategies and methods you like, for instance, `sed` is
a pretty good viable option for patching if you're familiar with it.

#### Patching toml files

I like to use a tool called [tomcli](https://git.sr.ht/~gotmax23/tomcli), which
is a bit confusing on the first try, I must admit, but it makes the patching
much easier once you get the hang of it.

Take a look at this example where I wanted to relax one of the dependencies for `python-sse-starlette`:

{% github_code https://github.com/r0x0d/python-sse-starlette/blob/main/python-sse-starlette.spec#L37-L38 %}

Another useful way of patching toml files with `tomcli` is like this:

```sh
# Patch pyproject.toml to use setuptools instead of uv_build (not available in Fedora)
tomcli set pyproject.toml lists str "build-system.requires" "setuptools>=61" "wheel"
tomcli set pyproject.toml str "build-system.build-backend" "setuptools.build_meta"
```

### Disclaimer: Using cookiecutter-fp template

Alternatively, you can get started using my cookiecutter packaging template.
This template is written in a way to help bootstrap some of your manual work
with copr/builds/logs by providing a set of crafted make targets for
convenience. You can read more about it in my blog post below:

{% post_card https://r0x0d.com/posts/packaging-for-fedora-general-purpose-cookiecutter-template %}

## Want to learn more? Joing the Matrix channel!

Did you find this interesting? Please, feel free to join the Fedora Matrix
channel to learn more and get started. The community is amazing and I'm pretty
sure that you will find what you need there.

- [Fedora Python](https://matrix.to/#/#python:fedoraproject.org)
- [Fedora AI/ML](https://matrix.to/#/#ai-ml:fedoraproject.org)

If you want to get in touch with me, just send a message to `@r0x0d:fedora.im`.

## References

- [RPM Packaging Guide](https://rpm-packaging-guide.github.io/)
