# x86-bandwidth-tool

Wu-Wei wrapper for the bandwidth benchmark (http://zsmith.co/bandwidth.html), which measures the speed of the memory hierarchy. It uses a modified version of the benchmark (https://github.com/elavoie/bandwidth/tree/wu-wei-tool) that adds commandline options to specify the range of data sizes to test with (to make the tests faster) and save cache information in a CSV format. 

It computes the max and min bandwidth obtained for tests using data sizes between (2/3) * CACHE_LEVEL_SIZE and  CACHE_LEVEL_SIZE (ex: between 24k and 32k for an L1 data cache of size 32k), or between 13 * LAST_LEVEL_CACHE_SIZE and  20 * LAST_LEVEL_CACHE_SIZE (ex: between ~50 MB and ~80 MB for an L3 cache of size 4 MB that interfaces with the main memory). These values were chosen to make sure the data is not resident in the previous cache layer to force memory transfer between different layers of the memory hierarchy.

The tests are run using the '--fast' option of the bandwidth tool, which uses 0.5 seconds per test. This is ten times faster than the default and provides good results on the machines I tested.

The test downloads a pre-compiled binary for supported platforms, currently linux 64-bit and macos 64-bit using wget.

To install:

    git clone git@github.com:elavoie/x86-bandwidth-tool
    cd x86-bandwidth-tool
    ./install

To obtain the bandwidth results in JSON format on standard output:
    
    ./run
    
The results are presented for each pair of memory level and type of instruction:

    {
        ...
        "L1-L2": {
            ...
            "Sequential 64-bit reads": {
                "units": "MB/s",
                 "max": 15647.2,
                "min": 12798.1
            },
            ...
        }
    }
    
In addition, information is provided on each memory level:
    
    {
        ...
        "L1": {
            "units": "B",
            "size": 32768
        }
        ...
    }
    
To add the results to an existing JSON object, which may for example compare the bandwidth used in a current benchark to the maximum bandwidth available on the platform:

    ./run '{ "foo": "bar" }'
    => { "foo": "bar", "bandwidth": { ... } }
