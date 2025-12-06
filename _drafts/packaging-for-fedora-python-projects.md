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

## Writing your first specfile

The art of writing good specfiles is something that you earn practicing, you
won't know out of the box all the features, tips and tricks, but you can get
started with easier ones. Let's see a couple of ways that we could get started.

### Manually crafting the specfile

Some times, starting from scratch and trying to manually build your first
specfile is always the best option. Let's analyze each section of a specfile
here to understand the components we have:

To make our lifes easier here, I will use as a reference the [example
specfile](https://docs.fedoraproject.org/en-US/packaging-guidelines/Python/#_example_spec_file)
from the Python Packaging Guidelines.

```
# Name of your package. For Python applications, it needs to be prefixed with
# `python-`
Name:           python-pello

# Version of your application
Version:        1.0.4

# The autorelease is a special macro that will identify which release to use
# based on your commit history
Release:        %autorelease

# Summary is where you gave a very brief description of the package under 80
# characters.
Summary:        Example Python library

# The license that the project uses. Check out
# https://docs.fedoraproject.org/en-US/packaging-guidelines/LicensingGuidelines/
# to see which licenses are available.
License:        MIT-0

# The URL from the upstream project. This can be either a pypi URL or
# github/gitlab/etc.
URL:            https://github.com/fedora-python/Pello

# Source is the definition of where the tarball/data will be pulled for your
# build
Source:         %{url}/archive/v%{version}/Pello-%{version}.tar.gz
```

### Using cookiecutter-fp template

Alternatively, you can get started using my cookiecutter packaging template.
This template is written in a way to help bootstrap some of your manual work
with copr/builds/logs by providing a set of crafted make targets for
convenience. You can read more about it in my blog post below:

{% post_card https://r0x0d.com/posts/packaging-for-fedora-general-purpose-cookiecutter-template %}


## Pulling sources from pypi or GitHub/Gitlab/etc?

### When to pull from pypi?

### When to pull from GitHub/Gitlab/etc?

## Patching in downstream

## Want to learn more? Joing the Matrix channel!

Did you find this interesting? Please, feel free to join the Fedora Matrix
channel to learn more and get started. The community is amazing and I'm pretty
sure that you will find what you need there.

- [Fedora Python](https://matrix.to/#/#python:fedoraproject.org)
- [Fedora AI/ML](https://matrix.to/#/#ai-ml:fedoraproject.org)

If you want to get in touch with me, just send a message to `@r0x0d:fedora.im`.



https://github.com/done-packaging/python-openapi-pydantic
