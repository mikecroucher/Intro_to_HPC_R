# Using Spark and Scala on the High Performance Computing (HPC) systems at Sheffield

## Presentation

The presentation given during the session is at [https://mikecroucher.github.io/Intro_to_HPC/#/](https://mikecroucher.github.io/Intro_to_HPC/#/)

## Description of Sheffield's HPC Systems

The University of Sheffield has two HPC systems:

* [SHARC](http://docs.hpc.shef.ac.uk/en/latest/sharc/) Sheffield's newest system. It contains about 2000 CPU cores all of which are latest generation.
* [Iceberg](http://docs.hpc.shef.ac.uk/en/latest/iceberg/) Iceberg is Sheffield's old system. It contains 3440 CPU cores but many of them are very old and slow.

The two systems are broadly similar but have small differences in the way you load applications using the module system.

**We recommend that you use ShARC as much as possible.**

## Connecting to the HPC systems from Windows

### Exercise 1: Install MobaXterm

To use the HPC from a Windows machine, you need a way to connect - we recommend you install **MobaXterm**.
This is available from [http://mobaxterm.mobatek.net](http://mobaxterm.mobatek.net).
On a University machine, you need to install the **Portable edition** (highlighted in the image below):

<img src="images/mobaXterm_download.png" />

The download is a zip file that contains three other files. You should **extract these files**, for example to your desktop, before you use them.
Do not run MobaXterm directly from the zip file.

**mobaXterm** also contains **mobaTextEditor** which you can use to write your programs.

### Exercise 2: Log in to ShARC

You can connect to ShARC using MobaXterm as shown in the screenshot below.
The `Remote Host` field should contain `sharc.sheffield.ac.uk`:

<img src="images/mobaxterm_sharc.png" />

If your log-in is successful, you should see something like the screen below.

<img src="images/sharc_login.png" />

If you **cannot see the file browser** pane on the left-hand side then see [Troubleshooting](#Troubleshooting)

At this point, you are on the **log in** or **Master node** of ShARC. There isn't much compute power here and many people use it simultaneously. As such, we should get onto a compute node as fast as possible.

<img src="images/log-in.gif" />

### Exercise 3: Start an interactive session on a compute node.

Since ShARC is a shared system, used by 100s of users, we need to request some resources from the **scheduler** using the command `qrshx`. We need to tell the system how much memory we want to use.

For example, to request 8 Gigabytes (8G) of memory, we would enter

```
qrshx -l rmem=8G
```

**Note: the `l` is a small letter `L` not the number `1`**

If this command is successful, you should see the prompt change from `sharc-login1` or  `sharc-login-2` to `sharc-nodeXXX` where XXX will be replaced with the number of the node you have been assigned.

<img src="images/worker_shell.png" />

You are now on a compute node and have access to your own CPU core and 8 Gigabytes of RAM.

Now would be a good time to learn some Linux commands using our [Mini Terminal Tutorial](terminal_tutorial.md)

### Exercise 4: Run R interactively

Once you are in a `qrshx` session, you can run R interactively.  
The system has several versions of R installed and you first have to select which one you want to use.
To see them all, execute the command 

```
module avail apps/R/
```

At the time of writing, this resulted in 

```
apps/R/3.3.2/gcc-4.8.5             apps/R/3.3.2/intel-17.0-sequential apps/R/3.4.0/gcc-4.8.5             apps/R/3.4.0/intel-17.0-sequential
apps/R/3.3.2/intel-17.0-parallel   apps/R/3.3.3/gcc-4.8.5             apps/R/3.4.0/intel-17.0-parallel
```

Let's look at what some of these versions mean

* `apps/R/3.3.2/gcc-4.8.5`  Version 3.3.2 of R compiled with gcc version 4.8.5
* `apps/R/3.4.0/gcc-4.8.5`  Version 3.4.0 of R compiled with gcc version 4.8.5
* `apps/R/3.3.2/intel-17.0-sequential`Version 3.3.2 of R compiled with Intel Compiler version 17 and the sequential version of the MKL (For use on one core)
* `apps/R/3.3.2/intel-17.0-parallel` Version 3.3.2 of R compiled with Intel Compiler version 17 and the parallel version of the MKL (For use on multiple cores)

The variables in use here are:

**R version**
We have several available.  If you need one that is not there, please [raise an issue on the HPC documentation site](https://github.com/rcgsheffield/sheffield_hpc/issues)

**Compiler version**
Much of R is written in C and C++, languages that need to be compiled before using them.  
The standard compiler suite on Linux machines such as ShARC is the gcc suite (The GNU Compiler Collection) but [several others exist](http://docs.hpc.shef.ac.uk/en/latest/sharc/software/development/index.html).
For everyday use, we suggest that you use one of the gcc versions since this will be very similar to the experience you have when using R on a 'normal' linux desktop.

Those versions of R that have been compiled using the commercial Intel Compilers are there for performance reasons.
Sometimes, they can be faster than the gcc versions but at the expense of being possibily incompatible with some packages.
Our advice is to first run your code with the gcc versions and, if performance is a problem, compare with the Intel version to see if it helps speed things up.

**Linear algebra libraries (e.g. MKL)**
The performance of many R functions depends on linear algebra routines that have been written in languages such as C, C++ and Fortran.  The most important of these are called BLAS and LAPACK which provide things such as Matrix-Matrix Multiplication, eigenvalue solvers and so on.

The speed of such libraries varies enormously.  The gcc versions of R on ShARC are built using versions of BLAS and LAPACK that ship with R itself.  These will not be the fastest versions available.

Our Intel versions of R are built using the commercial Intel Math Kernel Library (MKL) which can be extremely fast in many cases. To see how much faster the MKL is for Sheffield's old HPC system, Iceberg, [see this blog post](http://rse.shef.ac.uk/blog/intel-R-iceberg/).

**Choosing an R version and running it**

Once you've decided which version of R you want to use, execute the `module load` command to make it available.

```
module load apps/R/3.4.0/gcc-4.8.5
```

Then, run it by executing the command `R`

```
[us1e3r@sharc-node004 ~]$ R

R version 3.4.0 (2017-04-21) -- "You Stupid Darkness"
Copyright (C) 2017 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

[Previously saved workspace restored]

```

play around a little and then leave the session using `quit()`

### Exercise 5: Run an R program in batch mode

On a HPC system, interactive `qrshx` sessions should only be used for very short jobs. 
When we want to run long jobs or request resources such as multiple CPUs and huge amounts of memory, we should start using **batch processing**.

We have some example batch jobs in various languages in our `HPC_examples` github repo.
Execute the following command on ShARC.

```
git clone https://github.com/mikecroucher/HPC_Examples
```

Navigate to the R examples

```
cd HPC_Examples/languages/R
```

Go to the `hello_world`

```
cd hello_world
```

Take a look at the files that are there with the `ls` command

```
$ ls
hello.r  sharc_submit.sh
```

The files are

* hello.r - The R program we want to run
* The **batch submission script** sometimes called **job submission script**

Have a look at `hello.r` with the more command

```
more hello.r
```


Now look at the job submission script, `sharc_submit.sh` using the `more` command to see if you can understand it.
When you are ready, submit it to the queue with the `qsub` command

```
qsub sharc_submit.sh

Your job 83909 ("submit_to_sharc.sh") has been submitted
```

You can see the status of the queuing or running job with the `qstat` command.

```
qstat

job-ID  prior   name       user         state submit/start at     queue                          slots ja-task-ID 
-----------------------------------------------------------------------------------------------------------------      
 684688 0.00001 QRLOGIN    us1e3r       r     10/31/2017 11:25:31 all.q@sharc-node004.shef.ac.uk     1        
 684694 0.00000 sharc_subm us1e3r       qw    10/31/2017 11:31:45                                    1    
```

If you don't see your `sharc_submit.sh` job in `qstat`'s output this is probably because it's already finished running!  
The `QRLOGIN` job refers to your interactive session.

`qsub` and `qstat` are examples of **scheduler commands**.
A list of them can be found on the [HPC documentation website](http://docs.hpc.shef.ac.uk/en/latest/hpc/scheduler/index.html)

#### Where did the output go?

When the job has completed, you will see two new files in your current directory.
In my case, they were `sharc_submit.sh.e684694` and `sharc_submit.sh.o684694`
The number at the end refers to the `job-ID`

Look at these files with the `more` command

* The file that ends with `.e684694` contains the standard error stream (stderr)
* The file that ends with `.o684694` contains the standard output stream (stdout) 

Refer to the Wikipedia article on [standard streams](https://en.wikipedia.org/wiki/Standard_streams) for more information in this terminology.

#### Requesting a LOT of memory

Most of ShARC's nodes have 64Gb of RAM each. There are a small number with 256GB but these are heavily oversubscribed. 

Everyone who is part of the ooominds project has  [premium queue](http://rse.shef.ac.uk/resources/hpc/premium-hpc/) which includes access to nodes with up to 768GB of memory.  We also have powerful GPUs and nodes with up to 32 cores each.

If you need to request a lot of memory for your job - for example 250 Gigabytes per core - add the following lines to your job submission script:

```
# Tell the sysytem to make use of the project containing the big memory nodes
#$ -P rse
# Ask for 250 Gigabytes per core
#$ -l rmem=250G
```

## Troubleshooting

If after starting MobaXterm you cannot see the *file browser* pane on the left-hand side of the window then:

1. Close and restart MobaXterm;
1. **Session**;
1. Under **Advanced SSH settings** ensure that the **Use SCP protocol** box is ticked (see below);
1. Enter **Remote host** and **username** as before;
1. Click **OK** to connect.

<img src="images/MobaXterm-use-scp.png" />
