---
layout: post
title: "Put the portage tree on a diet ;-)"
description: "How to remove unused files from the tree"
category: gentoo
tags: [gentoo]
---
{% include JB/setup %}


<div class="alert alert-info">
<h4>Update</h4>
I've done a far improved way to do that! The story has a <a href="{% post_url 2014-01-19-portage-diet-part2 %}" class="alert-link">continue...</a>
</div>

Recently I've drammatically reduced the portage tree by getting rid of a bunch of categories I'm not going to use.
To do so, you have to add the following to `/etc/paludis/repositories/gentoo.conf`:

    sync_options = --exclude-from=/etc/paludis/repositories/gentoo-portage-exclude.list --rsync-option="--delete-excluded"

The `gentoo-portage-exclude.list` file is actually generated from `gentoo-portage-exclude.list.skel`
by the script below:

```bash
    #!/bin/bash
    #
    # Regenerate exclude list from the skeleton file
    #

    skel=`dirname $0`/gentoo-portage-exclude.list.skel
    out=`sed 's,\.skel,,' <<<${skel}`

    echo -n "Generating $out from $skel"

    # 0) put DNE header
    cat <<EOF >$out
    #
    # DO NOT EDIT! THIS FILE WAS GENERATED FROM ${skel}
    #
    EOF

    # 1) cat skeleton to a new file
    cat $skel >> $out

    # 2) append exclude list for metadata/md5-cache/* for every line
    # except for some special dirs and metadata.xml files
    cat ${skel} \
      | grep -v ' eclass/' \
      | grep -v ' licenses/' \
      | grep -v ' profiles/' \
      | grep -v 'metadata.xml' \
      | grep -v '^.*/\*$' \
      | sed 's,\([+\-]\) ,\1 metadata/md5-cache/,' \
      >>$out
```

And here is my _skeleton_ file w/ categories and profiles/architectures I'm not intended to use:

    #
    # Remove some categories I unlikely will use
    #
    - app-accessibility/***
    - app-antivirus/***
    - app-editors/emacs*/***
    - app-editors/gvim*/***
    - app-editors/qemacs*/***
    - app-editors/xemacs*/***
    - app-editors/vim*/***
    - app-emacs/***
    - app-forensics/***
    - app-i18n/man-pages-*/***
    - app-leechcraft/***
    - app-pda/***
    - app-vim/***
    - app-xemacs/***
    - dev-ada/***
    - dev-dotnet/***
    - dev-games/***
    - dev-haskell/***
    - dev-lisp/***
    - dev-lua/***
    - dev-ml/***
    - dev-tcltk/***
    - games-*/***
    - gnome-extra/***
    - gnustep-apps/***
    - gnustep-base/***
    - gnustep-libs/***
    - gpe-base/***
    - gpe-utils/***
    - lxde-base/***
    - media-plugins/vdr-*/***
    - media-radio/***
    - media-tv/***
    - media-video/vdr-*/***
    - net-dialup/***
    - net-news/***
    - net-nntp/***
    - net-zope/***
    - razorqt-base/***
    - rox-base/***
    - rox-extra/***
    - sci-biology/***
    - sci-calculators/***
    - sci-chemistry/***
    - sci-electronics/***
    - sci-misc/***
    - sci-physics/***
    - sec-policy/***
    - sys-cluster/***
    - sys-freebsd/***
    - sys-infiniband/***
    - virtual/emacs*/***
    - virtual/leechcraft*/***
    - x11-plugins/***
    - x11-wm/***
    - xfce-base/***
    - xfce-extra/***

    #
    # Do not sync some other (optional) stuff
    #
    - eclass/tests/***
    - licenses/***

    #
    # Remove architectures I don't use
    #
    - profiles/arch/alpha/***
    - profiles/arch/amd64-fbsd/***
    - profiles/arch/arm/***
    - profiles/arch/hppa/***
    - profiles/arch/ia64/***
    - profiles/arch/m68k/***
    - profiles/arch/mips/***
    - profiles/arch/powerpc/***
    - profiles/arch/s390/***
    - profiles/arch/sh/***
    - profiles/arch/sparc/***
    - profiles/arch/sparc-fbsd/***
    - profiles/arch/x86/***
    - profiles/arch/x86-fbsd/***
    - profiles/default/bsd/***
    - profiles/default/linux/alpha/***
    - profiles/default/linux/amd64/10.0/***
    - profiles/default/linux/amd64/13.0/desktop/gnome/***
    - profiles/default/linux/amd64/13.0/selinux/***
    - profiles/default/linux/amd64/dev/***
    - profiles/default/linux/arm/***
    - profiles/default/linux/hppa/***
    - profiles/default/linux/ia64/***
    - profiles/default/linux/m68k/***
    - profiles/default/linux/mips/***
    - profiles/default/linux/powerpc/***
    - profiles/default/linux/s390/***
    - profiles/default/linux/sh/***
    - profiles/default/linux/sparc/***
    - profiles/default/linux/x86/***
    - profiles/embedded/***
    - profiles/features/selinux/***
    - profiles/hardened/***
    - profiles/prefix/aix/***
    - profiles/prefix/bsd/***
    - profiles/prefix/darwin/***
    - profiles/prefix/hpux/***
    - profiles/prefix/linux/arm/***
    - profiles/prefix/linux/ia64/***
    - profiles/prefix/linux/ppc64/***
    - profiles/prefix/linux/x86/***
    - profiles/prefix/mint/***
    - profiles/prefix/sunos/***
    - profiles/prefix/windows/***
    - profiles/releases/10.0/***
    - profiles/releases/freebsd-*/***
    - profiles/targets/desktop/gnome/***
    - profiles/uclibc/***

This speedup sync a little, but the most important profit is I don't have __a lot__ of <strike>unused</strike>
small files on my system (SSD w/ zipped btrfs) drive anymore.
