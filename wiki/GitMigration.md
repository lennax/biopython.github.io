---
title: GitMigration
permalink: wiki/GitMigration
layout: wiki
---

We have recently migrated to git distributed version control (instead of
[CVS](CVS "wikilink")), as summarised [here](Git "wikilink").

The process of development of Biopython with git is outlined
[here](GitUsage "wikilink").

This page contains the information on the technicalities of the
transition itself and how the core developers should deal with the
changes during the transitional period.

Current setup
=============

[GitHub](http://github.com/) currently hosts the Biopython Git
repository at <http://github.com/biopython/biopython/>

This could become the official repository. Currently it is synchronized
with the main [CVS](CVS "wikilink") trunk every hour, so it should be up
to date most of the time.

Updates from CVS to github branch
---------------------------------

The updates are done with the [cvs2svn](http://cvs2svn.tigris.org/) tool
in the [cvs2git](http://cvs2svn.tigris.org/cvs2git.html) mode.

This works by extracting commits from the cvs repo and generating input
for [git
fast-import](http://www.kernel.org/pub/software/scm/git/docs/git-fast-import.html).
It seems to work nicely and is really fast.

The updates are performed hourly, using the following configuration
[1](http://bartek.rezolwenta.eu.org/biopython_git_update/). The scripts
obtains the cvs repository via rsync and performs the conversion
locally. This has to be done this way at least until we get git
installed on dev.open-bio.org.

After the conversion is done, the updated git branch is pushed to
github, so that others can use it. Afterwards, the copy of the git repo
is rsynced back to OBF servers for backup purposes.

Accepting code contributions
----------------------------

During the migration, the CVS repo is assumed to be still of higher
priority. This means that all code contributions need to go through CVS
and then get updated to the github branch. This effectively means, we
cannot push to the main biopython branch directly, but instead work on
different branches and generate diffs to be applied to CVS.

In case of small bug fixes, the core developers can continue to work
directly in CVS. The changes will get pushed to github eventually.

Since we also want to accept contributions through github, it means that
core developer integrating changes will need to do some extra work:

-   make sure you have an updated version of cvs source tree
-   make sure you have a git repo with the official and contributed
    branch
-   make a diff between the contributed and official branch in git repo
    (see [GitUsage](GitUsage "wikilink"))
-   apply this diff to the cvs repository
-   commit in cvs with appropriate message

Next steps
----------

Once we reach a consensus that git/github serves us well. We will make
the final switch. This would include:

-   dropping the cvs support (updating Biopython webpage)
-   shutting down the cvs2git update scripts
-   installing git on the open-bio servers
-   setting up a synchronization between obf-hosted branch and github
    branch
-   posting an announcement on the dev-mailing list, news server, and
    twitter account to celebrate!

