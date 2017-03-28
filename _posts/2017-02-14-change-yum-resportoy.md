---
layout: post
title: "SVN 合并分支"
categories:
- linus
---

### 资料 ###
1. [http://chunanyong.iteye.com/blog/697255](http://chunanyong.iteye.com/blog/697255)
2. svn help merge

### 合并 ###
A：被合并分支，from
B：合并到分支，to

##### 一、合并一个范围的版本
此类型应用最为广泛，主要是把分支中的修改合并到主干上来。在主干上点击右键选择合并，然后选择合并类型：合并一个范围的版本。
合并的源URL填写的是要合并的分支的URL，待合并的版本范围如果为空，则指的是合并分支上所有的版本，即自从分支创建以来到分支当前最新版本的所有演变。如果只是选择其中一个版本，或者几个版本，那么就表示只是将指定的n个版本的变化合并到主干上。如果只是选择其中一个版本，那么表示只是选择那个版本的修改，之前或之后的修改将不被采纳

**（不可选择版本）B合并A分支<font color="red">一个范围</font>的版本到工作集。合并结果A分支不变，B分支改变，工作集改变**

##### 二、复兴合并
复兴合并可以理解为是第一种合并类型的一种特例，在复兴合并中，主干可以理解为是自从开创分支之后没有任何修改，而分支是经过修改的，而且合并中分支是没有版本选择的。经过复兴合并，分支中所有的修改都会合并到主干中，合并的结果将使得分支和主干一模一样，从而可以删除分支。

**（不可选择版本）B合并A分支<font color="red">所有范围</font>的版本到工作集。合并结果AB、工作集一致。<font color="red">用于trunck合并branch后删除branch</font>**
    
##### 三、合并两个不同的树
此类型与前两种类型不同，第一种类型可以选择分支合并的版本，主干不能选择版本；第二种类型是主干和分支都不能选择合并的版本；而这种类型则是无论是主干还是分支都可以选择合并的版本，即可以选择过去的一个主干版本与分支的某个版本进行合并。合并的时候以选择的分支版本为主，如果选择的主干版本与分支版本有不同的地方，合并时主干部分将被放弃。
起始URL：选择主干目录的URL（应当和当前工作副本的URL一致，这个是所谓的合并点）
结束URL：选择要合并的分支的URL。
起始和结束的版本：一般起始版本应当找到最后一次同步时的版本，如果从没有同步过（第一次合并），则选择创建分支时的版本，结束版本一般是最新版本，如果你不想将某些内容合并进主干的话，也可以选择一个合并点。

### 细节 ####


        merge: Merge changes into a working copy.
        usage: 1. merge SOURCE[@REV] [TARGET_WCPATH]
                  (the 'complete' merge)
               2. merge [-c M[,N...] | -r N:M ...] SOURCE[@REV] [TARGET_WCPATH]
                  (the 'cherry-pick' merge)
               3. merge SOURCE1[@REV1] SOURCE2[@REV2] [TARGET_WCPATH]
                  (the '2-URL' merge)
        
          1. This form, with one source path and no revision range, is called
             a 'complete' merge:
        
               svn merge SOURCE[@REV] [TARGET_WCPATH]
        
             The complete merge is used for the 'sync' and 'reintegrate' merges
             in the 'feature branch' pattern described below. It finds all the
             changes on the source branch that have not already been merged to the
             target branch, and merges them into the working copy. Merge tracking
             is used to know which changes have already been merged.
        
             SOURCE specifies the branch from where the changes will be pulled, and
             TARGET_WCPATH specifies a working copy of the target branch to which
             the changes will be applied. Normally SOURCE and TARGET_WCPATH should
             each correspond to the root of a branch. (If you want to merge only a
             subtree, then the subtree path must be included in both SOURCE and
             TARGET_WCPATH; this is discouraged, to avoid subtree mergeinfo.)
        
             SOURCE is usually a URL. The optional '@REV' specifies both the peg
             revision of the URL and the latest revision that will be considered
             for merging; if REV is not specified, the HEAD revision is assumed. If
             SOURCE is a working copy path, the corresponding URL of the path is
             used, and the default value of 'REV' is the base revision (usually the
             revision last updated to).
        
             TARGET_WCPATH is a working copy path; if omitted, '.' is generally
             assumed. There are some special cases:
        
               - If SOURCE is a URL:
        
                   - If the basename of the URL and the basename of '.' are the
                     same, then the differences are applied to '.'. Otherwise,
                     if a file with the same basename as that of the URL is found
                     within '.', then the differences are applied to that file.
                     In all other cases, the target defaults to '.'.
        
               - If SOURCE is a working copy path:
        
                   - If the source is a file, then differences are applied to that
                     file (useful for reverse-merging earlier changes). Otherwise,
                     if the source is a directory, then the target defaults to '.'.
        
             In normal usage the working copy should be up to date, at a single
             revision, with no local modifications and no switched subtrees.
        
               - The 'Feature Branch' Merging Pattern -
        
             In this commonly used work flow, known also as the 'development
             branch' pattern, a developer creates a branch and commits a series of
             changes that implement a new feature. The developer periodically
             merges all the latest changes from the parent branch so as to keep the
             development branch up to date with those changes. When the feature is
             complete, the developer performs a merge from the feature branch to
             the parent branch to re-integrate the changes.
        
                 parent --+----------o------o-o-------------o--
                           \            \           \      /
                            \          merge      merge  merge
                             \            \           \  /
                 feature      +--o-o-------o----o-o----o-------
        
             A merge from the parent branch to the feature branch is called a
             'sync' or 'catch-up' merge, and a merge from the feature branch to the
             parent branch is called a 'reintegrate' merge.
        
               - Sync Merge Example -
                                         ............
                                        .            .
                 trunk  --+------------L--------------R------
                           \                           \
                            \                          |
                             \                         v
                 feature      +------------------------o-----
                                     r100            r200
        
             Subversion will locate all the changes on 'trunk' that have not yet
             been merged into the 'feature' branch. In this case that is a single
             range, r100:200. In the diagram above, L marks the left side (trunk@100)
             and R marks the right side (trunk@200) of the merge source. The
             difference between L and R will be applied to the target working copy
             path. In this case, the working copy is a clean checkout of the entire
             'feature' branch.
        
             To perform this sync merge, have a clean working copy of the feature
             branch and run the following command in its top-level directory:
        
                 svn merge ^/trunk
        
             Note that the merge is now only in your local working copy and still
             needs to be committed to the repository so that it can be seen by
             others. You can review the changes and you may have to resolve
             conflicts before you commit the merge.
        
               - Reintegrate Merge Example -
        
             The feature branch was last synced with trunk up to revision X. So the
             difference between trunk@X and feature@HEAD contains the complete set
             of changes that implement the feature, and no other changes. These
             changes are applied to trunk.
        
                            rW                   rX
                 trunk ------+--------------------L------------------o
                              \                    .                 ^
                               \                    .............   /
                                \                                . /
                 feature         +--------------------------------R
        
             In the diagram above, L marks the left side (trunk@X) and R marks the
             right side (feature@HEAD) of the merge. The difference between the
             left and right side is merged into trunk, the target.
        
             To perform the merge, have a clean working copy of trunk and run the
             following command in its top-level directory:
        
                 svn merge ^/feature
        
             To prevent unnecessary merge conflicts, a reintegrate merge requires
             that TARGET_WCPATH is not a mixed-revision working copy, has no local
             modifications, and has no switched subtrees.
        
             A reintegrate merge also requires that the source branch is coherently
             synced with the target -- in the above example, this means that all
             revisions between the branch point W and the last merged revision X
             are merged to the feature branch, so that there are no unmerged
             revisions in-between.
        
        
          2. This form is called a 'cherry-pick' merge:
        
               svn merge [-c M[,N...] | -r N:M ...] SOURCE[@REV] [TARGET_WCPATH]
        
             A cherry-pick merge is used to merge specific revisions (or revision
             ranges) from one branch to another. By default, this uses merge
             tracking to automatically skip any revisions that have already been
             merged to the target; you can use the --ignore-ancestry option to
             disable such skipping.
        
             SOURCE is usually a URL. The optional '@REV' specifies only the peg
             revision of the URL and does not affect the merge range; if REV is not
             specified, the HEAD revision is assumed. If SOURCE is a working copy
             path, the corresponding URL of the path is used, and the default value
             of 'REV' is the base revision (usually the revision last updated to).
        
             TARGET_WCPATH is a working copy path; if omitted, '.' is generally
             assumed. The special cases noted above in the 'complete' merge form
             also apply here.
        
             The revision ranges to be merged are specified by the '-r' and/or '-c'
             options. '-r N:M' refers to the difference in the history of the
             source branch between revisions N and M. You can use '-c M' to merge
             single revisions: '-c M' is equivalent to '-r <M-1>:M'. Each such
             difference is applied to TARGET_WCPATH.
        
             If the mergeinfo in TARGET_WCPATH indicates that revisions within the
             range were already merged, changes made in those revisions are not
             merged again. If needed, the range is broken into multiple sub-ranges,
             and each sub-range is merged separately.
        
             A 'reverse range' can be used to undo changes. For example, when
             source and target refer to the same branch, a previously committed
             revision can be 'undone'. In a reverse range, N is greater than M in
             '-r N:M', or the '-c' option is used with a negative number: '-c -M'
             is equivalent to '-r M:<M-1>'. Undoing changes like this is also known
             as performing a 'reverse merge'.
        
             Multiple '-c' and/or '-r' options may be specified and mixing of
             forward and reverse ranges is allowed.
        
               - Cherry-pick Merge Example -
        
             A bug has been fixed on trunk in revision 50. This fix needs to
             be merged from trunk onto the release branch.
        
                    1.x-release  +-----------------------o-----
                                /                        ^
                               /                         |
                              /                          |
                 trunk ------+--------------------------LR-----
                                                        r50
        
             In the above diagram, L marks the left side (trunk@49) and R marks the
             right side (trunk@50) of the merge. The difference between the left
             and right side is applied to the target working copy path.
        
             Note that the difference between revision 49 and 50 is exactly those
             changes that were committed in revision 50, not including changes
             committed in revision 49.
        
             To perform the merge, have a clean working copy of the release branch
             and run the following command in its top-level directory; remember
             that the default target is '.':
        
                 svn merge -c50 ^/trunk
        
             You can also cherry-pick several revisions and/or revision ranges:
        
                 svn merge -c50,54,60 -r65:68 ^/trunk
        
        
          3. This form is called a '2-URL merge':
        
               svn merge SOURCE1[@REV1] SOURCE2[@REV2] [TARGET_WCPATH]
        
             You should use this merge variant only if the other variants do not
             apply to your situation, as this variant can be quite complex to
             master.
        
             Two source URLs are specified, identifying two trees on the same
             branch or on different branches. The trees are compared and the
             difference from SOURCE1@REV1 to SOURCE2@REV2 is applied to the
             working copy of the target branch at TARGET_WCPATH. The target
             branch may be the same as one or both sources, or different again.
             The three branches involved can be completely unrelated.
        
             TARGET_WCPATH is a working copy path; if omitted, '.' is generally
             assumed. The special cases noted above in the 'complete' merge form
             also apply here.
        
             SOURCE1 and/or SOURCE2 can also be specified as a working copy path,
             in which case the merge source URL is derived from the working copy.
        
               - 2-URL Merge Example -
        
             Two features have been developed on separate branches called 'foo' and
             'bar'. It has since become clear that 'bar' should be combined with
             the 'foo' branch for further development before reintegration.
        
             Although both feature branches originate from trunk, they are not
             directly related -- one is not a direct copy of the other. A 2-URL
             merge is necessary.
        
             The 'bar' branch has been synced with trunk up to revision 500.
             (If this revision number is not known, it can be located using the
             'svn log' and/or 'svn mergeinfo' commands.)
             The difference between trunk@500 and bar@HEAD contains the complete
             set of changes related to feature 'bar', and no other changes. These
             changes are applied to the 'foo' branch.
        
                                   foo  +-----------------------------------o
                                       /                                    ^
                                      /                                    /
                                     /              r500                  /
                 trunk ------+------+-----------------L--------->        /
                              \                        .                /
                               \                        ............   /
                                \                                   . /
                            bar  +-----------------------------------R
        
             In the diagram above, L marks the left side (trunk@500) and R marks
             the right side (bar@HEAD) of the merge. The difference between the
             left and right side is applied to the target working copy path, in
             this case a working copy of the 'foo' branch.
        
             To perform the merge, have a clean working copy of the 'foo' branch
             and run the following command in its top-level directory:
        
                 svn merge ^/trunk@500 ^/bar
        
             The exact changes applied by a 2-URL merge can be previewed with svn's
             diff command, which is a good idea to verify if you do not have the
             luxury of a clean working copy to merge to. In this case:
        
                 svn diff ^/trunk@500 ^/bar@HEAD
        
        
          The following applies to all types of merges:
        
          To prevent unnecessary merge conflicts, svn merge requires that
          TARGET_WCPATH is not a mixed-revision working copy. Running 'svn update'
          before starting a merge ensures that all items in the working copy are
          based on the same revision.
        
          If possible, you should have no local modifications in the merge's target
          working copy prior to the merge, to keep things simpler. It will be
          easier to revert the merge and to understand the branch's history.
        
          Switched sub-paths should also be avoided during merging, as they may
          cause incomplete merges and create subtree mergeinfo.
        
          For each merged item a line will be printed with characters reporting the
          action taken. These characters have the following meaning:
        
            A  Added
            D  Deleted
            U  Updated
            C  Conflict
            G  Merged
            E  Existed
            R  Replaced
        
          Characters in the first column report about the item itself.
          Characters in the second column report about properties of the item.
          A 'C' in the third column indicates a tree conflict, while a 'C' in
          the first and second columns indicate textual conflicts in files
          and in property values, respectively.
        
            - Merge Tracking -
        
          Subversion uses the svn:mergeinfo property to track merge history. This
          property is considered at the start of a merge to determine what to merge
          and it is updated at the conclusion of the merge to describe the merge
          that took place. Mergeinfo is used only if the two sources are on the
          same line of history -- if the first source is an ancestor of the second,
          or vice-versa (i.e. if one has originally been created by copying the
          other). This is verified and enforced when using sync merges and
          reintegrate merges.
        
          The --ignore-ancestry option prevents merge tracking and thus ignores
          mergeinfo, neither considering it nor recording it.
        
            - Merging from foreign repositories -
        
          Subversion does support merging from foreign repositories.
          While all merge source URLs must point to the same repository, the merge
          target working copy may come from a different repository than the source.
          However, there are some caveats. Most notably, copies made in the
          merge source will be transformed into plain additions in the merge
          target. Also, merge-tracking is not supported for merges from foreign
          repositories.
        
        Valid options:
          -r [--revision] ARG      : ARG (some commands also take ARG1:ARG2 range)
                                     A revision argument can be one of:
                                        NUMBER       revision number
                                        '{' DATE '}' revision at start of the date
                                        'HEAD'       latest in repository
                                        'BASE'       base rev of item's working copy
                                        'COMMITTED'  last commit at or before BASE
                                        'PREV'       revision just before COMMITTED
          -c [--change] ARG        : the change made by revision ARG (like -r ARG-1:ARG)
                                     If ARG is negative this is like -r ARG:ARG-1
                                     If ARG is of the form ARG1-ARG2 then this is like
                                     ARG1:ARG2, where ARG1 is inclusive
          -N [--non-recursive]     : obsolete; try --depth=files or --depth=immediates
          --depth ARG              : limit operation by depth ARG ('empty', 'files',
                                     'immediates', or 'infinity')
          -q [--quiet]             : print nothing, or only summary information
          --force                  : force deletions even if deleted contents don't match
          --dry-run                : try operation but make no changes
          --diff3-cmd ARG          : use ARG as merge command
          --record-only            : merge only mergeinfo differences
          -x [--extensions] ARG    : Specify differencing options for external diff or
                                     internal diff or blame. Default: '-u'. Options are
                                     separated by spaces. Internal diff and blame take:
                                       -u, --unified: Show 3 lines of unified context
                                       -b, --ignore-space-change: Ignore changes in
                                         amount of white space
                                       -w, --ignore-all-space: Ignore all white space
                                       --ignore-eol-style: Ignore changes in EOL style
                                       -U ARG, --context ARG: Show ARG lines of context
                                       -p, --show-c-function: Show C function name
          --ignore-ancestry        : disable merge tracking; diff nodes as if related
          --accept ARG             : specify automatic conflict resolution action
                                     ('postpone', 'working', 'base', 'mine-conflict',
                                     'theirs-conflict', 'mine-full', 'theirs-full',
                                     'edit', 'launch')
                                     (shorthand: 'p', 'mc', 'tc', 'mf', 'tf', 'e', 'l')
          --reintegrate            : deprecated
          --allow-mixed-revisions  : Allow operation on mixed-revision working copy.
                                     Use of this option is not recommended!
                                     Please run 'svn update' instead.
          -v [--verbose]           : print extra information
        
        Global options:
          --username ARG           : specify a username ARG
          --password ARG           : specify a password ARG (caution: on many operating
                                     systems, other users will be able to see this)
          --no-auth-cache          : do not cache authentication tokens
          --non-interactive        : do no interactive prompting (default is to prompt
                                     only if standard input is a terminal device)
          --force-interactive      : do interactive prompting even if standard input
                                     is not a terminal device
          --trust-server-cert      : deprecated; same as
                                     --trust-server-cert-failures=unknown-ca
          --trust-server-cert-failures ARG : with --non-interactive, accept SSL server
                                     certificates with failures; ARG is comma-separated
                                     list of 'unknown-ca' (Unknown Authority),
                                     'cn-mismatch' (Hostname mismatch), 'expired'
                                     (Expired certificate), 'not-yet-valid' (Not yet
                                     valid certificate) and 'other' (all other not
                                     separately classified certificate errors).
          --config-dir ARG         : read user configuration files from directory ARG
          --config-option ARG      : set user configuration option in the format:
                                         FILE:SECTION:OPTION=[VALUE]
                                     For example:
                                         servers:global:http-library=serf



