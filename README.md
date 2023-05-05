Download Link: https://assignmentchef.com/product/solved-cuda-homework3-mpi-programming
<br>
The purpose of this assignment is to familiarize yourself with MPI programming.

In this problem, you need to use MPI to parallelize the following serial program (http://www. cs.nctu.edu.tw/~ypyou/courses/PP-f19/assignments/HW3/conduction.c). The program simluates heat conduction on an isolated 2D long strip and finds the minimum temperature until heat balance or reaching specified iterations, given random temperatures on the strip. There are three int arguments (<em>L</em>, which controls the length of the strip, <em>iteration </em>and <em>seed</em>, which seeds the random number generator) from command line arguments taken as inputs. For convenience, <em>L </em>is assumed to be divisible by the number of threads.

<table width="610">

 <tbody>

  <tr>

   <td width="610">#include &lt;stdio.h&gt;#include &lt;stdlib.h&gt;#ifndef W#define W 20                                                                      // Width#endifint main(int argc, char **argv) {int L = atoi(argv[1]);                                                           // Lengthint iteration = atoi(argv[2]);         // Iteration srand(atoi(argv[3]));      // Seedfloat d = (float) random() / RAND_MAX * 0.2; // Diffusivity int *temp = malloc(L*W*sizeof(int));            // Current temperature int *next = malloc(L*W*sizeof(int));            // Next time stepfor (int i = 0; i &lt; L; i++) { for (int j = 0; j &lt; W; j++) { temp[i*W+j] = random()&gt;&gt;3;}}int count = 0, balance = 0;while (iteration–) { // Compute with up, left, right, down pointsbalance = 1; count++;for (int i = 0; i &lt; L; i++) { for (int j = 0; j &lt; W; j++) {float t = temp[i*W+j] / d; t += temp[i*W+j] * -4;t += temp[(i – 1 &lt; 0 ? 0 : i – 1) * W + j]; t += temp[(i + 1 &gt;= L ? i : i + 1)*W+j]; t += temp[i*W+(j – 1 &lt; 0 ? 0 : j – 1)]; t += temp[i*W+(j + 1 &gt;= W ? j : j + 1)]; t *= d; next[i*W+j] = t ;if (next[i*W+j] != temp[i*W+j]) {balance = 0;</td>

  </tr>

  <tr>

   <td width="610">}} }if (balance) { break;}int *tmp = temp; temp = next; next = tmp;}int min = temp[0]; for (int i = 0; i &lt; L; i++) { for (int j = 0; j &lt; W; j++) { if (temp[i*W+j] &lt; min) {min = temp[i*W+j];}} }printf(“Size: %d*%d, Iteration: %d, Min Temp: %d
”, L, W, count, min); return 0;}</td>

  </tr>

 </tbody>

</table>

Hint: Divide array into several pieces and exchange minimum data to lower the overhead.

<h1>2           Requirement</h1>

<ul>

 <li>Your submitted solution contains one source file: c (or conduction.cpp).</li>

 <li>Your program takes two command-line arguments.</li>

 <li>You should not modify the output format.</li>

</ul>

<h1>3           Developing and Execution Environment of MPI Programs</h1>

<h2>3.1          Using the NCTU CS virtual cluster</h2>

<h3>3.1.1          Login information</h3>

Your program will run on these four machines/nodes which have NFS and NIS services that make MPI easy to use.

<table width="424">

 <tbody>

  <tr>

   <td width="115">IP</td>

   <td width="94">Port</td>

   <td width="108">User Name</td>

   <td width="108">Password</td>

  </tr>

  <tr>

   <td width="115">140.113.215.195</td>

   <td width="94">37031-37034</td>

   <td width="108">&lt;student ID&gt;</td>

   <td width="108">&lt;student ID&gt;</td>

  </tr>

 </tbody>

</table>

<h3>3.1.2          SSH from one machine to the others</h3>

Handy hostnames corresponding to their private IP are provided in /etc/hosts.

<table width="180">

 <tbody>

  <tr>

   <td width="79">Hostname</td>

   <td width="101">Original Port</td>

  </tr>

  <tr>

   <td width="79">pp01</td>

   <td width="101">37031</td>

  </tr>

  <tr>

   <td width="79">pp02</td>

   <td width="101">37032</td>

  </tr>

  <tr>

   <td width="79">pp03</td>

   <td width="101">37033</td>

  </tr>

  <tr>

   <td width="79">pp04</td>

   <td width="101">37034</td>

  </tr>

 </tbody>

</table>

For example, use the command below to log in to pp2 with the same user name.

$ ssh pp2

<h3>3.1.3          Executing jobs on other machines without entering a password</h3>

To make mpi work properly, you need to be able to execute jobs on remote nodes without typing a password. You will need to generate an ssh key by yourself.

You can also google “ssh passphrase” for details.

<table width="610">

 <tbody>

  <tr>

   <td width="610">$ mkdir -p ∼/.ssh$ ssh-keygen -t rsa# Then you will be prompted to enter some infomation, you can leave them empty for convenience.$ cat ∼/.ssh/id_rsa.pub &gt;&gt; ∼/.ssh/authorized_keys # Thanks to NFS, you only need to do it once.</td>

  </tr>

 </tbody>

</table>

Then, log in to all machines at least one time to make sure the configuration works and also record them in .ssh/known hosts file.

# Log in without entering password

$ ssh pp01

$ ssh pp02

$ ssh pp03

$ ssh pp04

<h2>3.2          MPI usage</h2>

MPI is installed in /home/PP-f19/MPI. Check with the command below.

$ /home/PP-f19/MPI/bin/mpiexec –version

<h3>3.2.1          Recording machines with host file</h3>

An MPI host file records all available machines with its ability. Attribute slots indicates at most what number of threads on that machine will be used by MPI.

// hostfile pp01 slots=4 pp02 slots=4 pp03 slots=4 pp04 slots=4

<h3>3.2.2          Compiling and running</h3>

You may get an MPI hello world program from https://reurl.cc/ekGDW and then compile and run the program on the four machines to make sure that MPI works well.

$ ls

hostfile mpi_hello_world.c

$ /home/PP-f19/MPI/bin/mpicc mpi_hello_world.c -o mpi_hello_world $ /home/PP-f19/MPI/bin/mpiexec -npernode 1 –hostfile hostfile mpi_hello_world

Hello world from processor ec037-031, rank 0 out of 4 processors

Hello world from processor ec037-032, rank 1 out of 4 processors

Hello world from processor ec037-033, rank 2 out of 4 processors Hello world from processor ec037-034, rank 3 out of 4 processors