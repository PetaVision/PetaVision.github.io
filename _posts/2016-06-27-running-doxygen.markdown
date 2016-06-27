---
layout: post
title:  "Updating doxygen-generated documentation"
author: Pete Schultz
categories: doxygen
---
_Updated version of [this blog post](http://petavision.github.io/doxygen/2015/08/19/running-doxygen.html)_.

Her is the procedure for updating the
[doxygen-generated webpages](http://petavision.github.io/doxygen/).  You will
need to have both the [OpenPV](https://github.com/OpenPV) and
[PetaVision.github.io](https://github.com/PetaVision.github.io) repositories
cloned on your local machine.  In the code below, these repositories are taken
to be located in `/path/to/OpenPV` and `/path/to/PetaVision.github.io`.

    cd /path/to/OpenPV/pv-core/docs/doxygen
    bash buildDoxygen.sh # Currently, this script contains the single command "doxygen cfg/doxy.cfg"
    
    cd /path/to/PetaVision.github.io
    git pull
    rsync -avzu --delete /path/to/OpenPV/docs/doxygen/html/ doxygen/
    # make sure to include the slashes at the end of the arguments, as they are meaningful to rsync.
    git add .
    git commit -m "Update to doxygen-generated pages"
    git push

The doxygen command in the buildDoxygen.sh script generates pages in
`/path/to/OpenPV/pv-core/docs/doxygen/html`.
This directory is in the `.gitignore` file, so running doxygen should not affect
the git-status of OpenPV.

The `rsync` command makes `/path/to/PetaVision.github.io/doxygen` into a copy of
`/path/to/OpenPV/pv-core/docs/doxygen/html`.  Note that for rsync, the slashes
at the end of the source and destination directories are significant.  If you leave
one or both off, the result will not be what you want.
