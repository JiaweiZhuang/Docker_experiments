# GEOS-Chem High Performance 12.1.0 precompiled code

Precompiled GEOS-Chem code for immediate use.
Also needs ~90GB input dataset (the ExtData/ folder). Can be downloaded from AWS cloud. See http://cloud.geos-chem.org.

Usage
-----

You can download and run this image using the following commands:

    docker pull zhuangjw/gchp_model
    input_path=/full/path/for/input/directory/on/host
    output_path=/full/path/for/output/directory/on/host
    docker run --rm -it -v $input_path:/ExtData -v $output_path:/OutputDir  zhuangjw/gchp_model
    mpirun -np 6 ./geos  # inside container, just execute model