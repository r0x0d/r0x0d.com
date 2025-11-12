---
title: toolbox, IDEs and atomic desktops
published: 2025-08-22T15:36:05.725Z
description: 'I have been experimenting a lot with fedora atomic and toolbox. I to share to detail my journey so far'
updated: ''
tags:
  - fedora
  - atomic
  - containers
draft: false
toc: true
lang: 'en'
image:
  path: /assets/imgs/toolbox.jpg
  alt: A phot of a toolbox
---
My experience so far with Fedora Atomic desktops (or ostree based desktops) has
been positive, of some sorte. First things first, there are a couple of
features that I like about such desktops, for example:

* It's easy to switch between desktop environments[^1]
* All updates are signed with GPG Keys, so you know that you're pulling from
trusted sources
* You can enable a systemd service to automatically update your system (In this
case, every day)

And a bunch more that I won't go into too much details. Whoever, you should
know that there are a couple of "bad ux" in such environments, more related to
development than anything else.

In short, I think ostree environments are really well suited for daily uses and
or cloud deployments (their original intention), regular users would probably
not run into such cases if they are mainly using the their computer for
browsing, gaming or basic text editting.

## Running text editors (i.e; IDEs)

One of the main deals with atomic desktops is the ability to run your favorite
text editor. If you are a developer, then you are probably more likely to bump
into such issues more frequently.

> For regular text editing, like reading a file or writing to a textual file,
> that should not be a problem.

Let's say that you're running vanilla Fedora and you usually install your
VSCode through the official Microsoft repository and follow all the
instructions, and in a couple of minutes, you have your development environment
partially ready. At least, in regards of text editing. Pretty simple, right?

When you make a switch to an ostree environment, you now face three options to
install the same tool.

1. You add VSCode using `rpm-ostree` and now you have a new package layer on
   top of your runnign system
2. You decide to try the flatpak version of the same application
3. You decide to go with toolbox/distrobox and install all the necessary tools
   inside it

While all of the three options are equally good, they have different approaches
to installing your development packages (python, go, rust, libs, etc..) and
interacting with your codebase.

## The first approach

For instance, if you choose the `1.` option, well, now you have a thirdy-party
package layered on top of your system and that will integrate very well with
most of the things, but, you still need to install the packages and libs
somehow, right? Well, you could either continue layering your system with all
the dependencies (but that's not quite why ostree is good at), or, find
yourself having a toolbox/distrobox container running that you install all of
those in there.

For this instance, I would say that you are still in the best shape out of the
three options, you are more likely to set up DevContainers and probably won't
notice much difference when using your tools. The downside, at least in my
opinion, is that now you have a development package layered on top of your OS
that should not really be there. While this is fine, it is not an approach that
I like. One of the advantages of ostree environments is that you have a
reproducible environment no matter where you go, and layered such packages can
break this experience with bad updates and all sort of other stuff.

## The second approach

Alright, but what about the `2.` approach then? The VSCode package,
specifically, does not really offer any support in their flatpak version,
actually, it is something maintained by a community of people, and not really
by Microsoft. While the folks that maintain such flatpak are amazing, you can't
(and should not) really expected frequent updates or more in-depth problem
solving.

For instance, flatpaks are really small containers that package all you need
inside it and let you execute the application in this "sandbox" env, and as you
can imagine, this makes things a bit complicated when you have a flatpak (that
is good mention, is read-only), being isolated and not being able to properly
access the development packages.

## The third approach

While this approach is one of the most recommended out there (and I use that
too), you might face problems such as, opening external programs if they are
not properly set up inide the container, or and some other minimal problems.

For instance, if you use VSCode and want to authenticated with GitHub to pull
your settings, you might need to do a couple of workarounds to get a firefox or
any other browser inside the container to launch it and authenticate.
Personally, I don't like this approach, and it seems that there isn't a very
good solution so far.

Personally, I have made the switch to `neovim` as I can install it inside my
toolbox container, mainly because I wanted to use `neovim` for a long time now,
but haven't had any good opportunity, as using VSCode or any other text editor
felt way more convenient.

## The power of toolbox (or even distrobox)

Toolbox is an amazing tool that you can spawn a container (either the default)
or a custom image that you made.It allow for so many customization and
possibility, and it is all sandboxed!

> Well, kinda of sandboxed. The toolbox container is created/ran with
> parameters that attach your `$HOME` folder and other devices to the container

In short, you can enable a very powerful development environment and experience
through toolbox usage and keep all the various packages individually contained
within each set of containers that I maintain.

# References

[^1]: [Trying different desktop environments using “rpm-ostree rebase”](https://fedoramagazine.org/trying-different-desktop-environments-using-rpm-ostree/)