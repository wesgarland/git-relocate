# Git-Relocate

This script allows developers to move files from one git repository to another, preserving history.

When individual files are moved, history is read with --follow; when directories are moved, this is not the case, so history will not be preserved across renames.  As relocation happens via patch files, it is best to move as many files at once as possible to avoid duplicate log messages in the destination repository.

## Installation

Stick this script somewhere in your path. If you cloned the repo and use a link, you'll be able to pull changes easily.

## Usage

```git-relocate [--dry-run] path/to/file/or/dir [path/to/file/or/dir ...] path/to/destination/repo```

--dry-run, if used, must be the first argument. It will cause this script to produce output as though it was working, but it will not modify your git repositories.

Once run, the source file(s) magically appear in your destination repo in the same directory-relative place as they were in the source repo. If this
is not where you wanted them, you'll need to `git mv` to get them there.  They will also be committed -- kind of like a cherry-picky -- so make sure
you're working on a branch or a repository you have cloned elsewhere, just in case something goes wrong.

The source files will be `git rm`'d from their repository, but this respository will not have these changes committed.  At the of a `git-mv` session, you should commit the repository to make the removals permanent.

*Note*: diff.context cannot be set to 0 in ~/.gitconfig or this script will fail

If multiple source files are specified and some do not exist, this script will terminate with exit code 2 but will still move as many files as it can. Failure to apply a patch to the destination repo will abort the script immediately, returning exit code 3.

_*All source files must belong to the same git repository.*_

### Example

```[~/git/dcp-worker] wes-linux-kds:wes# git-relocate --dry-run lib ../dcp-client
creating patch for /home/wes/git/dcp-worker -> /home/wes/git/dcp-client
 - lib/bindToTestHarness.js
 - lib/blessed-components/index.js
 - lib/blessed-components/log.js
 - lib/blessed-components/sandboxes.js
 - lib/check-scheduler-version.js
 - lib/console-patch.js
 - lib/service-repl.js
 - lib/standaloneWorker.js
 - lib/startWorkerLogger.js
 - lib/worker-loggers/common-types.js
 - lib/worker-loggers/console.js
 - lib/worker-loggers/dashboard.js
```

## What To Do If Something Goes Wrong
- Setting the environment variable GIT_RELOCATE_DEBUG_PATCH will leave the patch behind in the target directory as `_patch`.

If you see the error `fatal: previous rebase directory .git/rebase-apply still exists but mbox given.`, you may need to reset your target repository with `git am --abort`.
