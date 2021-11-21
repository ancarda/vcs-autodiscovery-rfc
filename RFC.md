# VCS Autodiscovery RFC

Published February 28th, 2020

## 1. Introduction

A large number of third party tools integrate into popular software forges such
as GitHub and GitLab, offering developers and their end-users abilities that
the forge itself does not implement. One common example is "README shields"
that integrate with a package hosting service, and displays license
information, download counts, and other statistics that are relevant to
developers and end-users.

Unfortunately, many of these tools support a limited number of well-known
forges - such as GitHub and GitLab - and do not support self-hosted instances
of these platforms. This increases friction for those wanting to self-host and
reduces the appeal of alternative platforms. Long term, this may pose a very
real threat to computing if all open source code is hosted by a few large
companies.

This RFC takes a step towards a standard that may, for a variety of tools, open
them up to being used on self-hosted instances of supported forges as well as
brand new players in the source code hosting space.

## 2. Terminology

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in
[BCP 14](https://tools.ietf.org/html/bcp14)
[[RFC2119]](https://tools.ietf.org/html/rfc2119)
[[RFC8174]](https://tools.ietf.org/html/rfc8174)
when, and only when, they appear in all capitals, as shown here.

## 3. Standard Variables

Section 4 describes meta tags that are used for this standard. The content
attribute MAY contain one or more variables, which are lowercase and surrounded
by `{` and `}` characters.

### 3.1. {ref}

`{ref}` is the name of a "branch" or a similar named reference concept.

### 3.2. {path}

`{path}` is the path to a file or directory without a leading forward slash.
The root directory MUST not be `/`, but rather an empty string.

Directories SHOULD end with a forward slash, files SHOULD NOT end with a
forward slash.

Valid examples:

* `docs/README.md` (file)
* `docs/` (directory)
* `` (root directory)

### 3.3. {line}

`{line}` is a line number or range of line numbers. Here, "number" means a
non-negative, non-zero integer. Valid formats include:

- A single number.
- A number range (example: `5-8`).
- A comma separated list of numbers (example: `5,7,8,9`).
- A comma separated list of ranges or numbers (example: `5,7-9`).

Software forges implementing a tag with this variable MUST support the single
number mode and SHOULD gracefully ignore ranges and lists if there is no
support for that.

Software forges MAY error if a range is in descending order (example: `8-5`) or
a list is in descending order (example: `9,8,7,5`).

## 4. Standard Tags

### 4.1. VCS

There is only one required meta tag, `vcs` which indicates the Version
Control System being used. The purpose of 4.1 is to facilitate repo cloning.

This tag MAY have the following values, but is not an exhaustive list. Software
MUST be able to gracefully handle unknown values:

* `bzr`    (GNU Bazaar)        bazaar.canonical.com
* `darcs`  (Darcs)             darcs.net
* `fossil` (Fossil)            fossil-scm.org
* `git`    (Git)               git-scm.com
* `hg`     (Mercurial)         mercurial-scm.org
* `pijul`  (Pijul)             pijul.org
* `svn`    (Apache Subversion) subversion.apache.org

Here is an example tag:

    <meta name="vcs" content="git" />

This tag MUST be used exactly once. Software consuming pages MUST error if this
tag is used more than once.

The content of this tag MUST not include spaces, commas, semicolons, or
anything to otherwise indicate a list. Multiple version control systems for the
same project (on the same HTML page) is not supported. Software consuming pages
SHOULD error if space, colon, or semicolon is present in the content attribute
of a `vcs` tag.

#### 4.1.1. Default Branch

If the version control system has the concept of "branches", where different
versions of the code may live, the `default-branch` key is used for this
purpose.

    <meta name="vcs:default-branch" content="master" />

#### 4.1.2. Clone URI

Software Forges MAY present one or more URIs using any scheme.

    <meta name="vcs:clone" content="https://rfc.example/project.git" />
    <meta name="vcs:clone" content="ssh://git@rfc.example:project" />

Clone URIs MAY use any scheme and MAY require authentication. If a repository
is open to the public, Forges SHOULD present at-least one clone URI that does
not require authentication. The unauthorized URI SHOULD be first.

Software that cannot understand a clone URI or was denied access SHOULD try the
subsequent URIs before failing.

### 4.2. Forge

Tags in 4.2 are to facilitate navigation around software forges.

#### 4.2.1. Path to File (Raw)

If the software forge allows a raw file to be retrieved with a deterministic
path, the `rawfile` key is used for this purpose. Here, "deterministic"
means software can construct a URL to the file without having to have knowledge
about the file including it's size, creation timestamp, or hash.

    <meta name="forge:rawfile"
          content="https://rfc.example/{path}?ref={ref}&raw=1" />

#### 4.2.2. Path to File (Pretty Printed)

If the software forge allows a file to be shown to an end-user with some pretty
printing, the `file` key is used for this purpose. Like `rawfile` (4.2.1), the
URI MUST be deterministic.

    <meta name="forge:file"
          content="https://rfc.example/{path}?ref={ref}" />

#### 4.2.3. Path to Explore Directory Contents (Pretty Printed)

    <meta name="forge:dir"
          content="https://rfc.example/{ref}/{path}" />

#### 4.2.4. Path to Project Summary

Links to an overview of the project. This may be hosted on a separate domain.

    <meta name="forge:summary" content="https://rfc.example/my-cool-project">

#### 4.2.5. Line

If the software forge supports pretty printing a file with a highlighted line,
it is indicated this way:

    <meta name="forge:line"
        content="https://rfc.example/tree/{ref}/item/{path}#L{line}">

The `{line}` variable is mandatory.
