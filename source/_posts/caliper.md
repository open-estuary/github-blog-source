title: Caliper
date: 2015-03-12 10:27:56
tags: Caliper
---
###Welcome to the Caliper wiki!
 
####What is Caliper?
Caliper is a test suite focused on functional integrity and performance of boards, it not only detects if the hardware and software on the board can work, but also tests the performance. Caliper can be run on boards to generate the test data, then converse the test data to scores by using a series of formulas so that the test data can be easily to read, thereby intuitively showing various performance values and helping to analyze the performance gaps of the boards.
Caliper’s ultimate goals: one hand is that it can generate a series of test data which provides inputs for optimization of specific boards or SoC platforms; the other hand, it helps to analyze the functional stability and performance capability of software solution (such as Estuary and Harmonic) on hardware platforms.

####How Caliper works?
Caliper mainly builds upon the existing open source benchmarks and test suites. It can be divided into the following items: functional testing and performance testing, each of them is known as a "System Test Item".  Caliper’s functional testing mainly use the LTP (Linux Test Project), which contains a lot of tests focused on systems’ functions and features.  Caliper’s performance assessment includes the following aspects: real-time, CPU, memory, network, file system and so on; any aspect of above referred is called "Test Sub-Items" herein. When Caliper test a Test Sub-Items, it needs to test many meaningful evaluable items which constitute the Test Sub-Item, this kind of evaluable items are called "Test Points" (such as the delay of the network). Small granularity division of a Test Point is "Test Cases", which is the minimum unit and cannot longer be divided. 

###How to Use Caliper?
The test suite mainly includes  performance test cases, it can be used to test the performance of machine. The test suite supports linux of x86_64, arm_32, arm_64. Here is steps to setup testbed.

####Work Mode

If the Host can access the Target with the ethernet, you only need to install the dependency environment in the Host and configure the client information (namely the Target information) in the Host. Also the Target and the Host can be the same machine if the Target can support the cross-compile instructions in your build scrips.

You need to install the follow softwares in your **Host or Target**, This depends on the work mode you selected as the follow:

    sudo apt-get install python-matplotlib

    sudo apt-get install python-numpy

    sudo apt-get install libyaml-dev

1. If the Host can ssh into the Target, you only need to install the above softwares in the Host. Then you shoud configure the **client_config.cfg** in the caliper, also need to copy the public key of the Host to the Target, so that the Host can access the Target without password. In this way, you only need to download and compile caliper on the Host, and then it will automatically copy the executive files to the target, and finish all execution. If the Compilation is on the Host, some requirements in environment need to be done in the Target, especially some dynamical libraries.

2. If the target borad have the compilation environment, you can directly download Caliper and compile it in the Target, then run it. Therefore, you should install the above softwares in the Target directly. After that you should configure the *client_config.cfg*, and set the client to be itself.


#####Host OS installation
It supports x86_64 CentOS6, OpenSUSE and Ubuntu platform, you need install 64bit CentOS system or Ubuntu system on your PC or server platform.
#####Toolchain installation

To build arm/android target binary, it requires arm/android toolchain deployment. We can download the existing compiled toolchains from some website.
Here is website to download ARM toolchains:
https://releases.linaro.org/13.10/components/toolchain/binaries/gcc-linaro-arm-linux-gnueabihf-4.8-2013.10_linux.tar.bz2,  which is for the target that is arm_32.
https://releases.linaro.org/13.10/components/toolchain/binaries/gcc-linaro-aarch64-linux-gnu-4.8-2013.10_linux.tar.bz2, which is for the target that is arm_64.

Note: Current building for x86_32 platform is not supported now.
####Download & Use Caliper
#####Download Caliper source code

    git clone http://github.com/HTSAT/caliper.git

This will download a directory named caliper in your current operation directory.
##### Enter the test suite

    cd caliper

#####Configure Caliper

Edit the file of **client_config.cfg** in the current directory, and edit the ip address, username, ssh port and password. The password is optional, you can leave it null, and ssh-copy the public of the Host to the Target, so that the Host can login the Target without password.

##### Run Caliper
If you have configured you environment, you only need to run the follow command to run caliper, it will finish the compile, execute commands and parser the output and get the summarization of the outputs.

The command is  './caliper'.

*Note: For cross compiler, the path of tool-chain need to be added in the $PATH in the Host.*
After the compilation is complete, you can view the compiled files. If the compiler option is `arm_64`, the generated files are in gen/arm_64 directory; if the option is `arm_32`, the generated files are in the folder of gen/arm_32 directory; if the compiler option is `x86_64`, then the generated files are in gen/x86_64 directory. Because we don't test Caliper in the machine which is x86_32 bit, so it don't support x86_32 yet.

After the command has been finished, the results generated are in the output folder, which is located in the 'gen' directory and contains all the results. Each benchmarks has two corresponding log files, named "XXX_output.log" and "XXX_parser.log"; also all benchmarks's result of selected points are stored in the yaml file, which located in directory of yaml. The command will generate the html file to show the result, the html contains some diagrams including the barchart and so on. The html file generated locates in the directory of 'output'.

##### Caliper output
You can see the comparision figures in output/html/Caliper_result.html, or you can see the specific values of Test Cases and scores in output/yaml/your_machine_name.yaml, output/yaml/your_machine_name_score.yaml and output/yaml/your_machine_name_score_post.yaml. They are all in the gen directory.

###### The Format of Yaml
    name: your_machine_name
    ......
    results:
      Performance:
        latency:
          process:
            Point_Scores:
              lat_proc_exec: 5.77899052273767
              lat_proc_fork: 5.950592290662938
              lat_proc_shell: 2.342564726762954
              lat_sig_catch: 2.8632263718748194
              lat_sig_install: 4.6958190604292955
              ......
        memory:
          bandwidth:
            Point_Scores:
              bw_mem_bcopy: 15.691400000000002
              bw_mem_bzero: 246.7238
              ......                                                          
        network:
          latency:
            Point_Scores:
              lat_connect: 0
              lat_pipe: 2.979769002361791
              ......
                                                            
The "Performance" means a Test Item; the "latency", "memory" and "network" are Test Sub-Items which belong to the "Performance"; In the Test Sub-Item of "memory", the "bandwidth" is a Test Point; in the Test Point of "bandwidth", some Test Cases has been tested, so in the "Point Scores"  has some key-value pairs, such as "bw_mem_bzero: 246.7238".
*Note: in some key-value pairs, the value of '0' means that the test case is failed.*

The format of scores of network and memory are similiar to the format of memory bandwidth.

Caliper can be expanded to include more benchmarks. In order to add benchmarks, you should know the code framework in Caliper first.

###Architecture & Contribute more benchmarks

####The architecture of Caliper

There are several files in the test suite, they are listed in the follow.

    benchmarks   client   common.py   __init__.py   README  server   
    test_cases_cfg   caliper   client_config.cfg   README.md   shadow_config.cfg

**benchmarks**: store the benchmarks, they can be downloaded or written by yourself.

**caliper**: run `./caliper`, the benchmarks which was configured in the 'test_cases_cfg/common_cases_def.cfg' and another corresponding configuration file will be compiled and generate the executable files in the build directory. Then the Host will scp the generated execution files to the Target, and then control the Target to run the commands and get the output of the command, then the host will parser the output. Caliper will run the command and parser the command one by one, 

**server**: This directory contains the scripts for dispatching the build, run and parser on the Host and remote login in the Target. Also part of scripts in 'server' directory will use the function in the directory named of 'client'. the thought of 'client' and 'server' is borrowed from the Autotest.

    .
    ├── build
    ├── common.py
    ├── compute_model
    ├── hosts
    ├── __init__.py
    ├── __init__.pyo
    ├── parser_process
    ├── run
    ├── test_host.py
    └── utils.py

The 'build' directory is for building the benchmarks in Caliper. The 'Compute_model' directory mainly include the method to get the score from the parser output, the method of scoring mainly in the scores_method.py. The 'parser_process' is mainly about the process of parsering the output of benchmarks, traverse the output to the score and draw the diagrams for Caliper. The 'run' directory mainly includes the test_run.py, it is the main code to execute the commands in benchmarks and the parser function defined by each benchmarks. The 'hosts' directory mainly contains the class of hosts and how to use hosts.

**test_cases_cfg**: benchmarks which will be compiled and run are defined in this directory. The tree of it is listed in the follow. 

    .
    ├── android
    ├── android_cases_def.cfg
    ├── arm
    ├── arm_cases_def.cfg
    ├── common
    ├── common_cases_def.cfg
    ├── README
    ├── server
    └── server_cases_def.cfg

The architecture of *common* directory looks like in the below.

    .
    ├── iperf
    │   ├── iperf_build.sh
    │   ├── iperf_parser.py
    │   ├── iperf_run.cfg
    │   └── iperf_server_run.cfg
    └── rttest
        ├── rttest_build.sh
        ├── rttest_parser.py
        └── rttest_run.cfg

If a benchmark will be added in the testsuite, the corresponding info should be added in the **test_cases_define.cfg**; in addtion, the build script, the configuration file of running it and parser file should be added in the directory which has the name of the benchmark.

####Add benchmarks to Caliper
If a benchmark be added in Caliper, some steps should be done.

##### 1. Define the benchmark

Add the corresponding information in **test_cases_cfg/test_cases_define.cfg**. The format of the info is listed below.

    [lmbench]
    version = 1
    download_cmd = git clone https://github.com/openssl/openssl.git
    build = lmbench_build.sh
    run = lmbench_run.cfg
    parser = lmbench_parser.py

The 'version' option is optional. If the benchmark is in the directory of "benchmarks" which locates in the root directory of Caliper, the 'download_cmd' is not needed; or you should configure the 'download_cmd' option.

*Notes: The 'download_cmd' option now only support the git command. In addition, the optios of 'build', 'run' and 'parser' are indispensable.*

#####2. 'Build' the benchmark
The script file which is specified by the 'build' option can compile the benchmark. The exsiting shell script of other benchmarks can be referenced. The path should be taken into consideration.

#####3. 'Run' the benchmark
The 'run' option illustrates the configuration of running the benchmark. The content of the configuration file is like this:

    [lmbench lat_sys_null]
    category = Performance latency process lat_sys_null
    scores_way =  exp_score_compute 1 -0.5
    command = lmbench/lat_syscall -P 2 null
    parser = syscall_latency_parser
    
Each item in the configuration is as a Test Case. The 'category' option set that the Test Case belongs to which category. The 'scores_way' option is set to compute the score of the Test Case. The method set in the'scores_way' can be found in 'scores_method.py' in the 'compute_model' directory which locates in 'script' directory. New computation method can be added in that file. The 'command' is to be run. The 'parser' option set the parser method to deal the output of the command, the parser method must be implement in the parser file.
 
#####4. 'Parser' the benchmark
The parser method has been set, and it must be implement, in the above example of code, the function of 'syscall_latency_parser' must be in the file of 'lmbench_parser.py'.

Notes: the funtion of parser must have two args; the first is the content which is the output of executing the command, the second is the file pointer to write the parser log file. In addtion the parser must return an float, which is needed for the later score computing.

*Notes: If the command is execcuted successfully, the function of parser must return a float number; or the 0 value should be returned.*

 
The existing test suite includes some test cases, but they have not been included by Test Points.
We will integrate the following benchmarks in the later versions.

####cpu performance
######    integer/float computing performance

benchmark cases: coremark/scimark/linpack/openssl

######instruction latency
 
   benchmark cases: lmbench
    
######instruction parallel (multi-issue/decoder instruction)
 
####memory performance
######L1 cache/L2 cache/memory performance

######hugetlb/uncachable/write-combine page attribute performance

######bus bandwidth, multi-thread read/write memory

     benchmark cases: memspeed, cachebench, stream, lmbench
  
####disk performance

######disk and filesystem performance

   benchmark cases: iozone
    
####network performance
 
######network configuration

 cases: ethtool
  
######network performance

  cases: iperf/netperf/tcpdump
