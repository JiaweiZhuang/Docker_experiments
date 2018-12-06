# GEOS-Chem classic 12.1.0 precompiled code

Precompiled GEOS-Chem code for immediate use.
Also needs ~80GB input dataset (the ExtData/ folder). Can be downloaded from AWS cloud. See http://cloud.geos-chem.org.

Usage
-----

You can download and run this image using the following commands:

    docker pull zhuangjw/gc_model
    input_path=/full/path/for/input/directory/on/host
    output_path=/full/path/for/output/directory/on/host
    docker run --rm -it -v $input_path:/ExtData -v $output_path:/OutputDir  zhuangjw/gc_model
    ./geos.mp  # inside container, just execute model