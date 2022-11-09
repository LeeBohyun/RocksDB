# ZNS 

##DB_bench Settings

```bash

sudo ./db_bench --fs_uri=zenfs://dev:nvme2n1 --benchmarks=fillrandom \
       --use_direct_reads --key_size=16 --value_size=800 \
       --target_file_size_base=2147483648 \
       --use_direct_io_for_flush_and_compaction \
       --max_bytes_for_level_multiplier=4 --write_buffer_size=2147483648 \
       --target_file_size_multiplier=10 --num=1000000 --threads=32 \
       --max_background_jobs=4 \
       -statistics \
       -stats_dump_period_sec=30 \
       -duration=1800 \
       -stats_interval_seconds=10 2>&1 | sudo tee result.txt
```

## blktrace
```bash
blktrace -d /dev/sda -o - | blkparse -i | tee blktrace.txt
```
