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
attribute MAY contain one or more variables, which are wrapped in a `%`
character and are lowercase.

### 3.1. %branch%

`%branch%` is the name of a "branch" or a similar named reference concept.

### 3.2. %path%

`%path%` is the path to a file or directory without a leading slash. For
instance, `docs/README.md` (file) or `docs/` (directory).

## 4. Standard Tags

### 4.1. VCS

There is only one required meta tag, `vcs` which indicates the Version
Control System being used. This tag MAY have the following values, but is not
an exhaustive list. Software MUST be able to gracefully handle unknown values:

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

### 4.2. Default Branch

If the version control system has the concept of "branches", where different
versions of the code may live, the `default-branch` key is used for this
purpose.

    <meta name="vcs:default-branch" content="master" />

### 4.3. Path to File (Raw)

If the software forge allows a raw file to be retrieved with a deterministic
path, the `rawfile` key is used for this purpose. Here, "deterministic"
means software can construct a URL to the file without having to have knowledge
about the file including it's size, creation timestamp, or hash.

    <meta name="vcs:rawfile"
          content="https://rfc.example/%path%?ref=%branch%&raw=1" />

### 4.4. Path to File (Pretty Printed)

If the software forge allows a file to be shown to an end-user with some pretty
printing, the `file` key is used for this purpose. Like `rawfile` (4.3), the
URI MUST be deterministic.

    <meta name="vcs:file"
          content="https://rfc.example/%path%?ref=%branch%" />

### 4.5. Path to Explore Directory Contents (Pretty Printed)

    <meta name="vcs:dir"
          content="https://rfc.example/%branch%/%path%" />

### 4.6. Clone URI

Software Forges MAY present one or more URIs using any scheme.

    <meta name="vcs:clone" content="https://rfc.example/project.git" />
    <meta name="vcs:clone" content="ssh://git@rfc.example:project" />

Clone URIs MAY use any scheme and MAY require authentication. If a repository
is open to the public, Forges SHOULD present at-least one clone URI that does
not require authentication. The unauthorized URI SHOULD be first.

Software that cannot understand a clone URI or was denied access SHOULD try the
subsequent URIs before failing.
