---
layout: post
title: 'Packaging for Fedora: woolly! Checking the dependency hell'
tags:
- fedora
- packaging
- woolly
- tooling
image:
  path: "/assets/imgs/woolly.jpg"
  alt: A cute lamb in a green field
date: 2025-12-10 09:56 -0300
---
If you've ever been interested in bringing software to Fedora that you use and
thought would be interesting for the community, but were unsure about what
you'd need to package to make it work, fear not!
[woolly](https://github.com/r0x0d/woolly) is here to help with this arduous
process!

## What is woolly?

In general, **woolly** is a project I started due to a demand from my work at
Red Hat. My team had a requirement to deliver two large projects on Fedora:

* [Goose](https://github.com/block/goose)
* [Linux MCP Server](https://github.com/rhel-lightspeed/linux-mcp-server)

Some people on the team already had experience in packaging, but others did
not.

Both projects mentioned above bring with them several dependencies (which are
often not present in the Fedora repositories), therefore, it becomes a
challenge for experienced and amateur packagers to discover the complete list
of dependencies that will be necessary to bring from the upstream projects to
downstream.

So, from this need, **woolly** was born.

If you've ever had to package something for Fedora (or any other Linux
distribution), you know how painful that task can be. An endless sea of
dependencies, projects that don't configure their dependencies as `optional`,
etc. All of this contributes to the difficulty of identifying and putting
together an action plan to start this journey, especially for projects that are
still active, since the list of dependencies can change considerably depending
on the maintainers.

In short, **woolly** can discover the dependencies needed for your project, and
tell you exactly what already exists in the Fedora repositories (which versions
are present, too!), what's missing, and generate reports that can facilitate
your (or your team's) understanding.


## It looks perfect! How do I get the woolly?

**woolly** is available from [pypi](https://pypi.org/project/woolly),
therefore, a simple:

```sh
pip install woolly

# or

pipx install woolly
```

Will get you to the latest version.

## How do I use woolly?

Using **woolly** is very easy! The user interface was designed to be intuitive
and simple, so we don't want distractions, but rather useful information! Take
a look at the following example:


```sh
woolly check serde
```

The above command will produce the following output:

```sh
Analyzing Rust package: serde
Registry: crates.io
Cache directory: /home/r0x0d/.cache/woolly

  Analyzing Rust dependencies ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% • 0:00:05 complete!

Dependency Summary for 'serde' (Rust)
╭────────────────────────────┬───────╮
│ Metric                     │ Value │
├────────────────────────────┼───────┤
│ Total dependencies checked │    10 │
│ Packaged in Fedora         │     7 │
│ Missing from Fedora        │     0 │
╰────────────────────────────┴───────╯

Dependency Tree:
serde v1.0.228 • ✓ packaged (1.0.225, 1.0.228)
└── serde_core v1.0.228 • ✓ packaged (1.0.225, 1.0.228)
    └── serde_derive v1.0.228 • ✓ packaged (1.0.225, 1.0.228)
        ├── proc-macro2 v1.0.103 • ✓ packaged (1.0.101, 1.0.103)
        │   └── unicode-ident v1.0.22 • ✓ packaged (1.0.19, 1.0.22)
        ├── quote v1.0.42 • ✓ packaged (0.3.15, 1.0.40, 1.0.41)
        │   └── proc-macro2 v1.0.103 • ✓ (already visited)
        └── syn v2.0.111 • ✓ packaged (1.0.109, 2.0.106, 2.0.111)
            ├── proc-macro2 v1.0.103 • ✓ (already visited)
            └── unicode-ident v1.0.22 • ✓ (already visited)

Log saved to: /home/r0x0d/.local/state/woolly/logs/woolly_20251210_091241.log
```

See how simple it is to use **woolly** to check the dependencies of a given
upstream project? `Rust` is used by default in **woolly** since the project was
born from the need to check the dependencies of projects in `Rust`, but it also
has support for `Python`.


```sh
woolly check -l py flask
```

And the output is:

```sh
Analyzing Python package: flask
Registry: PyPI
Cache directory: /home/r0x0d/.cache/woolly

  Analyzing Python dependencies ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% • 0:00:07 complete!

    Dependency Summary for 'flask'
               (Python)
╭────────────────────────────┬───────╮
│ Metric                     │ Value │
├────────────────────────────┼───────┤
│ Total dependencies checked │    13 │
│ Packaged in Fedora         │    11 │
│ Missing from Fedora        │     0 │
╰────────────────────────────┴───────╯

Dependency Tree:
flask v3.1.2 • ✓ packaged (3.1.2)
├── blinker v1.9.0 • ✓ packaged (1.9)
├── click v8.3.1 • ✓ packaged (8.1.7)
│   └── colorama v0.4.6 • ✓ packaged (0.4.6)
├── importlib-metadata v8.7.0 • ✓ packaged (8.7)
│   ├── zipp v3.23.0 • ✓ packaged (3.23)
│   └── typing-extensions v4.15.0 • ✓ packaged (4.15)
├── itsdangerous v2.2.0 • ✓ packaged (2.2)
├── jinja2 v3.1.6 • ✓ packaged (3.1.6)
│   └── markupsafe v3.0.3 • ✓ packaged (3.0.2)
├── markupsafe v3.0.3 • ✓ (already visited)
└── werkzeug v3.1.4 • ✓ packaged (3.1.3)
    └── markupsafe v3.0.3 • ✓ (already visited)

Log saved to: /home/r0x0d/.local/state/woolly/logs/woolly_20251210_091548.log
```

### Showing only optional dependencies

**Woolly** also has support for displaying the optional dependencies of
projects, as this can be useful to check to find out which extras you can
enable for your project.


```sh
woolly check -l py flask --optional
```

Obviously, the command above takes much longer to execute, since the project
may have several optional dependencies to be consulted:

```sh
Analyzing Python package: flask
Including optional dependencies
Registry: PyPI
Cache directory: /home/r0x0d/.cache/woolly

  Analyzing Python dependencies ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% • 0:00:01 complete!

    Dependency Summary for 'flask'
               (Python)
╭────────────────────────────┬───────╮
│ Metric                     │ Value │
├────────────────────────────┼───────┤
│ Total dependencies checked │    41 │
│ Packaged in Fedora         │    10 │
│ Missing from Fedora        │     0 │
│                            │       │
│ Optional dependencies      │    27 │
│   ├─ Packaged              │     2 │
│   └─ Missing               │     0 │
╰────────────────────────────┴───────╯

Dependency Tree:
flask v3.1.2 • ✓ packaged (3.1.2)
├── blinker v1.9.0 • ✓ packaged (1.9)
├── click v8.3.1 • ✓ packaged (8.1.7)
│   └── colorama (max depth reached)
├── importlib-metadata v8.7.0 • ✓ packaged (8.7)
│   ├── zipp (max depth reached)
│   ├── typing-extensions (max depth reached)
│   ├── pytest (optional) (max depth reached)
│   ├── importlib-resources (optional) (max depth reached)
│   ├── packaging (optional) (max depth reached)
│   ├── pyfakefs (optional) (max depth reached)
│   ├── flufl-flake8 (optional) (max depth reached)
│   ├── pytest-perf (optional) (max depth reached)
│   ├── jaraco-test (optional) (max depth reached)
│   ├── sphinx (optional) (max depth reached)
│   ├── jaraco-packaging (optional) (max depth reached)
│   ├── rst-linker (optional) (max depth reached)
│   ├── furo (optional) (max depth reached)
│   ├── sphinx-lint (optional) (max depth reached)
│   ├── jaraco-tidelift (optional) (max depth reached)
│   ├── ipython (optional) (max depth reached)
│   ├── pytest-checkdocs (optional) (max depth reached)
│   ├── pytest-ruff (optional) (max depth reached)
│   ├── pytest-cov (optional) (max depth reached)
│   ├── pytest-enabler (optional) (max depth reached)
│   └── pytest-mypy (optional) (max depth reached)
├── itsdangerous v2.2.0 • ✓ packaged (2.2)
├── jinja2 v3.1.6 • ✓ packaged (3.1.6)
│   ├── markupsafe (max depth reached)
│   └── babel (optional) (max depth reached)
├── markupsafe v3.0.3 • ✓ packaged (3.0.2)
├── werkzeug v3.1.4 • ✓ packaged (3.1.3)
│   ├── markupsafe (max depth reached)
│   └── watchdog (optional) (max depth reached)
├── asgiref v3.11.0 (optional) • ✓ packaged (3.8.1)
│   ├── typing-extensions (max depth reached)
│   ├── pytest (optional) (max depth reached)
│   ├── pytest-asyncio (optional) (max depth reached)
│   └── mypy (optional) (max depth reached)
└── python-dotenv v1.2.1 (optional) • ✓ packaged (1.1)
    └── click (optional) (max depth reached)
```

> Whenever you check the optional dependencies of a project, remember
> to use the `--max-depth` parameter to limit the analysis, otherwise,
> **woolly** will happily check all the optional dependencies of everything
> 😅
{: .prompt-warning }

### Showing missing only dependencies

Following the same idea as `optional` dependencies, **woolly** also has the
ability to show only what is missing from the Fedora repositories:

```sh
woolly check -l py cyclopts -m
```

```sh
Analyzing Python package: cyclopts
Registry: PyPI
Cache directory: /home/r0x0d/.cache/woolly

  Analyzing Python dependencies ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% • 0:00:00 complete!

  Dependency Summary for 'cyclopts'
               (Python)
╭────────────────────────────┬───────╮
│ Metric                     │ Value │
├────────────────────────────┼───────┤
│ Total dependencies checked │    12 │
│ Packaged in Fedora         │     9 │
│ Missing from Fedora        │     2 │
╰────────────────────────────┴───────╯

Missing packages that need packaging:
  • cyclopts
  • rich-rst

Log saved to: /home/r0x0d/.local/state/woolly/logs/woolly_20251210_093409.log
```

This option is useful for large projects, since the list of dependencies
can reach 100+, so, using the `--missing-only` parameter, we have
a view of only what is missing.

### Generating reports

One of the coolest features we have with **woolly** is the ability to generate
reports in different formats, allowing you to customize how you see the results
of your project's dependencies!

| Format   | Description                                        | Aliases            |
| ---------| -------------------------------------------------- | :----------------- |
| stdout   | Rich console output (default)                      | console, terminal  |
| markdown | Markdown report file                               | md                 |
| json     | JSON report file (machine-readable)                | -                  |
| template | Custom template-based report (requires --template) | tpl, jinja, jinja2 |

## Contributing to woolly

All types of contributions are very welcome to the project! If you are
interested in contributing, whether with code, documentation, or even reporting
problems, please feel free to do so through the repository at:

{% github r0x0d/woolly %}
