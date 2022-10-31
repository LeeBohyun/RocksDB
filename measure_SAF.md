# Measuring Space Amplification in RocksDB

## Overview
This week, we are going to calculate space amplification in RocksDB by running db_bench.



## Instructions

### 1. Run DB_Bench
```bash
./db_bench --benchmarks="readrandomwriterandom" \
        -db="/home/lbh/rocksdb-data" \
        -use_direct_io_for_flush_and_compaction=true \
        -use_direct_reads=true \
        -target_file_size_base=2097152 \
        -write_buffer_size=2097152 \
        -max_bytes_for_level_base=33554432 \
        -max_bytes_for_level_multiplier=3 \
        -duration=1200 \
        -statistics \
        -stats_dump_period_sec=30 \
        -stats_interval_seconds=10 2>&1 | tee result.txt
```
- `--benchmarks="readrandomwriterandom"`: 1 writer, N threads doing random reads
- `-db="/home/lbh/rocksdb-data"`: The path of RocksDB data directory
- `-use_direct_io_for_flush_and_compaction=true`: Use O_DIRECT for background flush and compaction I/O
- `-use_direct_reads=true`: Use O_DIRECT for reading data
- `-compaction_style=0`: style of compaction; level-based (0), universal(1)
    - **You should update this value to change the compaction style**
- `-write_buffer_size=2097152`: Number of bytes to buffer in memtable before compacting
    - **You should change this value according to the capacity of your system**
- `-max_bytes_for_level_base=16777216`: Max bytes for level-1
    - **You should change this value according to the capacity of your system**
- `-max_bytes_for_level_multiplier=10`: A multiplier to compute max bytes for level-N (N >= 2)
    - **You should change this value according to the capacity of your system**
- `-duration=1200`: Time in seconds for the random-ops tests to run
- `-statistics`: Database statistics
- `-stats_dump_period_sec=30`: Gap between printing stats to log in seconds
- `-stats_interval_seconds=10`: Report stats every N second

### 2. Calculate space amplification

> I recommend to run the benchmark until the level increases more than **2**.

In the RocksDB ```LOG``` file, you can get the below stat:

```bash
$ vim /path/to/rocksdb-data
...
** Compaction Stats [default] **
Level    Files   Size     Score Read(GB)  Rn(GB) Rnp1(GB) Write(GB) Wnew(GB) Moved(GB) W-Amp Rd(MB/s) Wr(MB/s) Comp(sec) CompMergeCPU(sec) Comp(cnt) Avg(sec) KeyIn KeyDrop Rblob(GB) Wblob(GB)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  L0      2/0    1.82 MB   0.5      0.0     0.0      0.0       1.2      1.2       0.0   1.0      0.0     87.3     14.64             13.69      1406    0.010       0      0       0.0       0.0
  L1      1/0   15.99 MB   1.0      4.1     1.2      2.8       3.9      1.1       0.0   3.1     72.7     69.5     57.15             53.19       351    0.163     63M  2808K       0.0       0.0
  L2      1/0   61.43 MB   0.4      4.3     1.0      3.3       3.3      0.0       0.0   3.2     87.2     67.1     50.55             46.96        57    0.887     70M    15M       0.0       0.0
 Sum      4/0   79.24 MB   0.0      8.4     2.3      6.1       8.4      2.4       0.0   6.8     70.0     70.6    122.35            113.85      1814    0.067    134M    18M       0.0       0.0
 Int      0/0    0.00 KB   0.0      0.0     0.0      0.0       0.0      0.0       0.0   4.5     61.1     74.9      0.27              0.26         6    0.046    256K    11K       0.0       0.0
 ...
```

Space amplification = (L1 size + L2 size) / L2 size


# References
- https://github.com/meeeejin/SWE3033-F2021
