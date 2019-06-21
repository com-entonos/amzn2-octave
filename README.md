# amzn2-octave
minimum octave for amazon linux 2 ami

Copyright (c) 2019 Entonos. All rights reserved.

customer wanted to move matlab workflow to aws for scale but concerned about total cost. profiling typical workflow in octave suggested most of the time was spent in glpk, BLAS/LAPACK and fftw. a proof-of-concept resulted in the following minimum octave build (cli only, ~530MB install directory, ~90MB compressed) and almost 3x faster than the octave in repo and at least a wash with matlab.

should be easy to add additional libraries if required, e.g. arpack, sundials, etc (add to 'to_build_lib_oct')

octave hardcodes full paths during installation so you need to settle on one place to install octave and stick with that when you bake the octave install in a container/AMI.

<b>summary:</b> install glpk, fftw, openblas and octave to directory [default is /shared/octave] for a given processor type. 

		./to_build [prefix [processor_type]]

where 'prefix' is the installation directory and 'processor_type' needs to be defined by both 'gcc -march=' option and in openBLAS's TargetList.txt (see 'to_build_lib_oct').
if 'processor_type' > compiling machine, then tests for openBLAS and Octave will fail. the default 'processor_type' = native (see 'to_build').


<b>quick:</b> on running amazon linux 2 instance (vm or ec2) with internet access, then e.g.
	
		git clone https://github.com/com-entonos/amzn2-octave.git && cd amzn2-octave
		./to_build /opt/octave
		export PATH:$PATH:/opt/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/octave/lib
		octave --texi-macros-file /dev/null
		
		
<b>archive:</b> on running amazon linux 2 instance (vm or ec2), then e.g. on c4.xlarge
	
		git clone https://github.com/com-entonos/amzn2-octave.git && cd amzn2-octave && \
		./to_build /opt/octave_c4 && tar -cJf octave_c5.tar.xz -C /opt octave_c4

	
<b>execute node:</b> Bake into AMI or container, e.g. on c4.xlarge
	
		yum update -y
		yum install -y libgfortran
		tar -xf octave_c4.tar.xz -C /opt
		export PATH:$PATH:/opt/octave_c4/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/octave_c4/lib
		octave --texi-macros-file /dev/null --eval "2+3"


NOTES:

0) if you have matlab \*.mexa64 files, place them in 'mex/' and we'll try to squish them into octave (better to make native \*.oct files if possible)
1) may need to edit 'pkg/to_build' to include required octave packages (PKGS) and provide a matlab/octave test in 'pkg/test/test.m'
2) check \*.log files for possible problems even if no failures
3) if native < processor_type checks may fail 
4) if processor_type=native (default) then libraries and octave will be compiled for the native machine (e.g. AWS's c5, c4, c3, m5, ...)

when third-party \*.mexa64 files were 'squished' into octave > v4.2.2, rewarded with a runtime error ("free(): invalid next size (fast)"). that's why octave v4.2.2 is default, otherwise octave v5.1.0 is fine by itself (last line of 'to_build_lib_oct').

start to finish: ~35 minutes on AWS c4.xlarge; ~60 minutes under vmware (4 cores, 8GB; amzn2-vmware_esx-2.0.20190313-x86_64.xfs.gpt.ova) on macOS 10.13.6 with 2.2GHz Core i7 Sandy Bridge (2720QM). Equivalently, optimized build on spot instances c3.xlarge, c4.xlarge, c5.xlarge, m3.xlarge, m4.xlarge and t3.xlarge for a total cost of $0.32.
