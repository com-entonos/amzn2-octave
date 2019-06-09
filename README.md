# amzn2-octave
minimum octave for amazon linux 2 ami

build glpk, ffftw, lapack and octave to relocatable directory [default is ${PWD}/local/octave]

<b>quick:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2) with internet access, then
	
		./to_build
		export PATH:$PATH:${PWD}/local/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:${PWD}/local/octave/lib
		octave --texi-macros-file /dev/null
		
		
<b>archive:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2), then e.g.
	
		./to_build /shared/octave && tar -cJf octave.tar.xz -C /shared octave

	
<b>execute node:</b> Bake into AMI or container, e.g.
	
		yum update -y
		yum install -y libgfortran
		tar -xf octave.tar.xz -C /shared
		export PATH:$PATH:/shared/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/shared/octave/lib
		octave --texi-macros-file /dev/null --eval "2+3"


NOTES:

0) if you have matlab \*.mexa64 files, place them in 'mex/' and we'll try to squash them into octave
1) may need to edit 'pkg/to_build' to include required octave packages (PKGS) and provide a matlab/octave test in 'pkg/test/test.m'
2) if other than avx/avx2, edit 'to_build_oct_lib' (ARCH and fftw configure options)
3) check \*.log files for possible problems even if no failures
3)  ...
4) profit

start to finish: ~80 minutes under vmware (4 cores, 6GB; amzn2-vmware_esx-2.0.20190313-x86_64.xfs.gpt.ova) on macOS 10.13.6 with 2.2GHz Core i7 Sand Bridge (2720QM)
