
:tocdepth: 1

Introduction
============

This document, based off DMTN-0023 written by Jim Bosch, provides a brief tutorial for using the LSST Software Stack's main command-line input (CLI) drivers.  We write in a more condensed style, trying to get users rapidly up to speed on how to use the CLI commands to process raw HSC data.

We assume the needed parts of the DM stack, `ci_hsc`_, ``astrometry_net_data``, and the `obs_subaru`_  camera pkg have been set up prior to using this tutorial.

.. _obs_subaru: https://github.com/lsst/obs_subaru

.. _obs_sdss: https://github.com/lsst/obs_sdss

.. _obs_cfht: https://github.com/lsst/obs_cfht

.. _obs_decam: https://github.com/lsst/obs_decam

.. _obs_lsstSim: https://github.com/lsst/obs_lsstSim

.. _ci_hsc: https://github.com/lsst/ci_hsc

.. _data-repository-setup:

Data Repository Setup
=====================

We assume that the raw data and master calibration frames are both already available.  In fact, `ci_hsc`_ already includes everything we'll need for later processing.

We also assume the location of `ci_hsc`_ is in the environment variable ``$CI_HSC_DIR``.

Start by creating a ``DATA`` directory, which will be the root of what we call a *data repository*:

.. prompt:: bash

  mkdir DATA

The files within this directory will be managed by an object called the *butler*, which abstracts all of our I/O; under normal circumstances, files and directories in a data repository should only be accessed or modified using the butler, not directly by hand. 

The structure of the data repository is defined by another class called a *mapper*.  Most mappers are defined in an *obs* package, which lets us use the native organization for each instrument (at least for raw data).

Depending on how you cloned the repo, the mapper file may already exist, but if not, tell the butler which mapper a data repository uses as so:

.. prompt:: bash

  echo "lsst.obs.hsc.HscMapper" > DATA/_mapper

Next, *ingest* the raw images into the data repository, using the ``ingestImages.py`` script:

.. prompt:: bash

  ingestImages.py DATA $CI_HSC_DIR/raw/*.fits --mode=link

The ``--mode=link`` flag adds symlinks for every file in the raw directory to the appropriate (butler-managed) location in the DATA directory.

This step also creates a *registry:* a binary database file listing all the raw images in the repository.  It should be in the DATA dir under the name ``registry.sqlite3``.

Calibration frames are typically stored in a separate data repository, and `ci_hsc`_ already contains a complete set of these in the CALIB dir.

Make a symlink from the DATA directory into the CALIB repository:

.. prompt:: bash

  cd DATA

.. prompt:: bash

  ln -s $CI_HSC_DIR/CALIB .

This location will automatically be searched by pipelines when looking for calibration data. 

