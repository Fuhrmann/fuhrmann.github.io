---
title: "Sources for my spec files on Fedora"
date: 2022-04-01T00:00:00+00:00
author: Ricardo Fuhrmann
layout: post
permalink: /sources-for-my-spec-files-fedora/
---

These are my spec files of some apps that are not available for Fedora. The specs are used in [copr](https://copr.fedorainfracloud.org/coprs/fuhrmann) to build the packages.

### Useful links

* [RPKG quick start](https://docs.pagure.org/rpkg-util/quick_start.html#new-project)
* [Preparing software for packaging](https://rpm-packaging-guide.github.io/#preparing-software-for-packaging)
* [My COPR profile](https://copr.fedorainfracloud.org/coprs/fuhrmann)
* [Packaging: SourceURL](https://fedoraproject.org/wiki/Packaging:SourceURL)
* [Downloading sources at build](https://stackoverflow.com/questions/33177450/how-do-i-get-rpmbuild-to-download-all-of-the-sources-for-a-particular-spec)
* [Packaging problems and solutions](https://wiki.mageia.org/en/Packaging_problems_and_solutions)
* [Creating a basic Spec File](https://docs.fedoraproject.org/en-US/Fedora_Draft_Documentation/0.1/html/Packagers_Guide/sect-Packagers_Guide-Creating_a_Basic_Spec_File.html)

## Available COPRs

These are the available COPRs and how to use them:

### i3-gaps

To install the most recent version of [i3-gaps](https://github.com/Airblader/i3):

```shell
sudo dnf copr enable fuhrmann/i3-gaps
sudo dnf install i3-gaps
```

To build locally:

```shell
rpkg local --spec i3-gaps.spec
```

Link to COPR: [fuhrmann/i3-gaps](https://copr.fedorainfracloud.org/coprs/fuhrmann/i3-gaps)

--------------

### git-friendly

To install the most recent version of [git friendly](https://github.com/git-friendly/git-friendly)

```shell
sudo dnf copr enable fuhrmann/git-friendly
sudo dnf install git-friendly
```

To build locally:

```shell
rpkg --spec git-friendly.spec
```

Link to COPR: [fuhrmann/git-friendly](https://copr.fedorainfracloud.org/coprs/fuhrmann/git-friendly)

--------------

### ctop

To install the most recent version of [ctop](https://github.com/bcicen/ctop)

```shell
sudo dnf copr enable fuhrmann/ctop
sudo dnf install ctop
```

To build locally:

```shell
rpkg --spec ctop.spec
```

Link to COPR: [fuhrmann/ctop](https://copr.fedorainfracloud.org/coprs/fuhrmann/ctop)
