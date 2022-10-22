# Measuring Space Amplification in RocksDB

## Overview
This week, we are going to calculate space amplification in RocksDB by running db_bench.



## Instructions

### 1. Run DB_Bench
```bash
./db_bench --benchmarks="readrandomwriterandom" \
        -db="/home/mijin/swe3033/rocksdb-data" \
        -use_direct_io_for_flush_and_compaction=true \
        -use_direct_reads=true \
        -target_file_size_base=2097152 \
        -write_buffer_size=2097152 \
        -max_bytes_for_level_base=33554432 \
        -max_bytes_for_level_multiplier=5 \
        -duration=3600 \
        -statistics \
        -stats_dump_period_sec=30 \
        -stats_interval_seconds=10 2>&1 | tee result.txt
```
