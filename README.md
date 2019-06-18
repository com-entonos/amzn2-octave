# amzn2-octave
minimum octave for amazon linux 2 ami

customer had a matlab script which depends on some 3rd party \*.mexa64 files. profiling suggested most of the time was spent in glpk, BLAS/LAPACK and fftw. they wanted to move the script to aws and wanted to avoid paying matlab licensing. therefore, it was decided to do a minimum install of octave (w/o gui). this resulted in the following minimum octave build (~530MB install directory) and almost 3x faster than the equivalent matlab.

should be easy to add additional libraries if required, e.g. arpack, sundials, etc 

it appears even the most current version of octave hardcode paths during installation so you need to settle on one place to install octave and stick with that when you bake the octave install in a container/AMI.

<b>summary:</b> install glpk, fftw, openblas and octave to directory [default is /shared/octave]. 

		./to_build [prefix] [process_type]

where \[prefix] is the installation directory and \[process_type] needs to be defined by both 'gcc -march=' option and in openBLAS's TargetList.txt (see 'to_build_lib_oct').
if \[process_type] > compiling machine, then tests for openBLAS and Octave will fail. the default \[processor_type] = haswell (see to_build) so we have avx2.


<b>quick:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2) with internet access, then
	
		./to_build /opt/octave
		export PATH:$PATH:/opt/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/octave/lib
		octave --texi-macros-file /dev/null
		
		
<b>archive:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2), then e.g.
	
		./to_build /opt/octave_haswell haswell && tar -cJf octave_haswell.tar.xz -C /opt octave_haswell

	
<b>execute node:</b> Bake into AMI or container, e.g.
	
		yum update -y
		yum install -y libgfortran
		tar -xf octave_haswell.tar.xz -C /opt
		export PATH:$PATH:/opt/octave_haswell/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/octave_haswell/lib
		octave --texi-macros-file /dev/null --eval "2+3"


NOTES:

0) if you have matlab \*.mexa64 files, place them in 'mex/' and we'll try to squash them into octave (better to make native \*.oct files if possible)
1) may need to edit 'pkg/to_build' to include required octave packages (PKGS) and provide a matlab/octave test in 'pkg/test/test.m'
2) if other than avx/avx2, edit ARCH in 'to_build' (and check fftw configure options in 'to_build_oct_lib')
3) check \*.log files for possible problems even if no failures
4) checks may fail if native < ARCH
5)  ...
6) profit

when i 'squashed' the third-party \*.mexa64 files into octave > v4.2.2, rewarded with a runtime error ("free(): invalid next size (fast)"), so this customer needed to stay with v4.2.2 (since they couldn't provide updated \*.mexa64 files), otherwise octave v5.1.0 is fine by itself.

start to finish: ~60 minutes under vmware (4 cores, 8GB; amzn2-vmware_esx-2.0.20190313-x86_64.xfs.gpt.ova) on macOS 10.13.6 with 2.2GHz Core i7 Sandy Bridge (2720QM)
