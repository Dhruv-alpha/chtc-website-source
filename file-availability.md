---
highlighter: none
layout: file_avail
title: File Availability Options
---


HTCondor File Transfer
======================

HTCondor file transfer is the standard solution for file portability,
and is built in to HTCondor job scheduling. You can see HTCondor file
transfer as introduced in our \"Intro to Running HTCondor Jobs\" and in
the [HTCondor
Manual.](http://research.cs.wisc.edu/htcondor/manual/v7.8/2_5Submitting_Job.html#SECTION00354000000000000000)

Contents
--------

1.  [Applicability](#Appli)
2.  [Transferring Input Files](#input)
3.  [Transferring Output Files](#output)

<a name="Appli"></a>

**1. Applicability**
----------------

* Intended use:  
Good for delivering any type of data to jobs, but with file-size
limitations (see below). Remember that you can/should split up a large
input file into many smaller files for cases where each job only needs a
portion of the data. By default, the submit file \"executable\",
\"output\", \"error\", and \"log\" files are ALWAYS transferred.

* Advantages:  
HTCondor\'s file transfer is robust and is available on ANY of CHTC\'s
accessible HTC resources (including the UW Grid of campus pools, and the
Open Science Grid).

* Input File Limitations:  
HTCondor\'s file transfer can cause issues for submit server performance
when too many jobs are transferring too much data at the same time.
Therefore, HTCondor file transfer is only good for input files up to
\~20 MB per file IF the number of concurrently-queued jobs will be 100
or greater. Even when individual files are small, there are issues when
the total amount of input data per-job approaches 500 MB. For cases
beyond these limitations, one of our other CHTC file delivery methods
should be used. Remember that creating a \*.tar.gz file of directories
and files can give your input and output data a useful amount of
compression.

* Output File Limitations:  
Because jobs are less likely to be completing at the same time, total
job output size of up to 1 GB will not cause submit server performance
issues, but it\'s always advantageous to create a \*.tar.gz file of all
desired output before job completion (and to also delete the un-tar\'d
files so they are not also transferred back).

* Data Security:  
Files transferred with HTCondor transfer are owned by the job and
protected by user permissions in the CHTC pool. When signaling your jobs
to run on the UW Grid (Flocking) or the Open Science Grid (Glidein),
your files will exist on someone else\'s computer only for the duration
of each job. Please feel free to email us if you have data security
concerns regarding HTCondor file transfer, as encryption options are
available.


<a name="input"></a>

**2. Transferring Input Files**
---------------------------

Simply add the \"transfer\_input\_files\" line to your submit file, like
so:

``` {.sub}
transfer_input_files = file1,../file2,/home/username/file3,dir1,dir2/
```

Note: By default, the submit file \"executable\", \"output\", and
\"error\" files are ALWAYS transferred.

### Notes:

-   DO NOT use \"transfer\_input\_files\" for files within /squid or
    /staging as doing so will create severe performance issues for your
    jobs and those of other users. (Similarly, you should never submit
    jobs from within /squid or /staging.)
-   Comma-separated files and directories to-be-transferred should be
    listed with a path relative to the submit directory, or can be
    listed with the absolute path(s), as shown above for \"file3\". The
    submit file \"executable\" is automatically transferred and does not
    need to be listed in \"transfer\_input\_files\".
-   All files that are transferred to a job will appear within the top
    of the working directory of the job, regardless of how they are
    arranged within directories on the submit server.
-   A whole directory and it\'s contents will be transferred when listed
    without the \"/\" after the directory name. When a directory is
    listed with the \"/\" after the directory name, only the directory
    contents will be transferred.
-   Jobs will be placed on hold by HTCondor if any of the files or
    directories do not exist (or if you have a typo).
-   See more about [file transfer in the HTCondor
    Manual.](http://research.cs.wisc.edu/htcondor/manual/v7.8/2_5Submitting_Job.html#SECTION00354000000000000000)

<a name="output"></a>

**3. Transferring Output Files**
----------------------------

**HTCondor will automatically transfer back ALL new or modified files to
the submit directory.** This automatically includes the \"output\" and
\"error\" files indicated in the submit file.

### Notes:

-   Output files transferred back to the submit server will appear in
    the initial submit directory of the job.
-   HTCondor does not automatically transfer back new directories.
    Therefore, it is a best practice to have your job(s) create a
    \*.tar.gz file for desired output directories, so that the \*.tar.gz
    file gets transferred AND so that it is compressed.
-   It is important to have your job remove all unwanted \"new\" files
    before job completion, so that these are NOT transferred back as
    perceived output. This includes files that have arrived in the job
    working directory via an alternate file delivery method.
-   It is possible to list only your desired output files by using
    \"transfer\_output\_files\" in the submit file; however, if you have
    a typo in a filename or if any of the files are not created during
    the job, the job will be placed on hold and ALL output lost (which
    can be a pain). Therefore, \"transfer\_output\_files\" should be
    used with caution, or for jobs that always first create these output
    files (perhaps empty), even if the job later exits early with an
    error.
