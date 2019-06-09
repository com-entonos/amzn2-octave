# amzn2-octave
minimum octave for amazon linux 2 ami

build glpk, ffftw, lapack and octave to relocatable [default is ${PWD}/local/octave]

<b>quick:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2) with internet access, then
	
		./to_build
		export PATH:$PATH:local/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:local/octave/lib
		octave --texi-macros-file /dev/null
		
		
<b>archive:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2), then
	
		./to_build <prefix_dir>/octave && tar -cJf octave.tar.xz -C <install_dir> octave

	
<b>execute node:</b> Bake into AMI or container
	
		yum update -y
		yum install -y libgfortran
		tar -xf octave.tar.xz -C <prefix_dir>
		export PATH:$PATH:<prefix_dir>/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<prefix_dir>/octave/lib
		octave --texi-macros-file /dev/null
		
NOTES:

1) may need to edit pkg/to_build to include required octave packages and provide a test in pkg/test
2) if other than avx/avx2, edit to_build_oct_lib (ARCH and fftw configure options)
3)   ...
4) profit
