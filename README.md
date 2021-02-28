# VCS Autodiscovery

VCS structure auto-discovery through meta tags

## What and Why

This project holds the RFC for VCS Autodiscovery; meta tags that help software
understand different software forges. Given a URL, such as:

    https://vcs-autodiscovery.example/project/

Software can scrape `<meta>` tags in the `<head>` tag to learn how to clone or
navigate this project. These tags look like:

    <meta name="vcs" value="git">

    <meta name="vcs:clone"
          value="https://vcs-autodiscovery.example/project.git">

## Standard

Read the latest standard in RFC.md

Discuss the RFC here: <https://lists.sr.ht/~ancarda/vcs-autodiscovery>

## Known Implementations

Known implementations are in IMPLEMENTATIONS.md. If you write software that
produces or consumes VCS Autodiscovery meta tags, please send a patch to add
yourself to IMPLEMENTATIONS.md

You may want to subscribe to the mailing list, where changes to the RFC will
be discussed.
