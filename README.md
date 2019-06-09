# amzn2-octave
minimum octave for amazon linux 2 ami

build glpk, ffftw, lapack and octave to relocatable [default is ${PWD}/local/octave] and uses -mtune=skylake for avx/avx2 (see to_build_oct_lib)

<b>quick:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2) with internet access, then
	
		./to_build
		export PATH:$PATH:local/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:local/octave/lib
		octave --texi-macros-file /dev/null
		
		
<b>archive:</b> copy (recursively) contents of this directory to running amazon linux 2 instance (vm or ec2), then
	
		./to_build <prefix_dir>/octave && tar -cJf octave.tar.xz -C <install_dir> octave

	
<b>execute node:</b> Bake into AMI or container
	
		yum install -y libgfortran
		tar -xf octave.tar.xz -C <prefix_dir>
		export PATH:$PATH:<prefix_dir>/octave/bin
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<prefix_dir>/octave/lib
		octave --texi-macros-file /dev/null
		

