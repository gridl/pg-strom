@ja{
<h1>GUCパラメータ</h1>

本節ではPG-Stromの提供する設定パラメータについて説明します。
}
@en{
<h1>GUC Parameters</h1>

This session introduces PG-Strom's configuration parameters.
}

@ja{
# 特定機能の有効化/無効化

|パラメータ名                   |型    |初期値|説明       |
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.enabled`             |`bool`|`on` |PG-Strom機能全体を一括して有効化/無効化する。|
|`pg_strom.enable_gpuscan`      |`bool`|`on` |GpuScanによるスキャンを有効化/無効化する。|
|`pg_strom.enable_gpuhashjoin`  |`bool`|`on` |HashJoinによるGpuJoinを有効化/無効化する。|
|`pg_strom.enable_gpunestloop`  |`bool`|`on` |NestLoopによるGpuJoinを有効化/無効化する。|
|`pg_strom.enable_gpupreagg`    |`bool`|`on` |GpuPreAggによる集約処理を有効化/無効化する。|
|`pg_strom.enable_partitionwise_gpujoin`|`bool`|`on`|GpuJoinを各パーティションの要素へプッシュダウンするかどうかを制御する。PostgreSQL v10以降でのみ対応。|
|`pg_strom.enable_partitionwise_gpupreagg`|`bool`|`on`|GpuPreAggを各パーティションの要素へプッシュダウンするかどうかを制御する。PostgreSQL v10以降でのみ対応。|
|`pg_strom.pullup_outer_scan`   |`bool`|`on` |GpuPreAgg/GpuJoin直下の実行計画が全件スキャンである場合に、上位ノードでスキャン処理も行い、CPU/RAM⇔GPU間のデータ転送を省略するかどうかを制御する。|
|`pg_strom.pullup_outer_join`   |`bool`|`on` |GpuPreAgg直下がGpuJoinである場合に、JOIN処理を上位の実行計画に引き上げ、CPU⇔GPU間のデータ転送を省略するかどうかを制御する。|
|`pg_strom.enable_numeric_type` |`bool`|`on` |GPUで`numeric`データ型を含む演算式を処理するかどうかを制御する。|
|`pg_strom.cpu_fallback`        |`bool`|`off`|GPUプログラムが"CPU再実行"エラーを返したときに、実際にCPUでの再実行を試みるかどうかを制御する。|
|`pg_strom.nvme_strom_enabled`  |`bool`|`on` |SSD-to-GPUダイレクトSQL実行機能を有効化/無効化する。|
|`pg_strom.nvme_strom_threshold`|`int` |自動 |SSD-to-GPUダイレクトSQL実行機能を発動させるテーブルサイズの閾値を設定する。|
}

@en{
# Enables/disables a particular feature

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.enabled`             |`bool`|`on` |Enables/disables entire PG-Strom features at once|
|`pg_strom.enable_gpuscan`      |`bool`|`on` |Enables/disables GpuScan|
|`pg_strom.enable_gpuhashjoin`  |`bool`|`on` |Enables/disables GpuJoin by HashJoin|
|`pg_strom.enable_gpunestloop`  |`bool`|`on` |Enables/disables GpuJoin by NestLoop|
|`pg_strom.enable_gpupreagg`    |`bool`|`on` |Enables/disables GpuPreAgg|
|`pg_strom.enable_partitionwise_gpujoin`|`bool`|`on`|Enables/disables whether GpuJoin is pushed down to the partition children. Available only PostgreSQL v10 or later.|
|`pg_strom.enable_partitionwise_gpupreagg`|`bool`|`on`|Enables/disables whether GpuPreAgg is pushed down to the partition children. Available only PostgreSQL v10 or later.|
|`pg_strom.pullup_outer_scan`   |`bool`|`on` |Enables/disables to pull up full-table scan if it is just below GpuPreAgg/GpuJoin, to reduce data transfer between CPU/RAM and GPU.|
|`pg_strom.pullup_outer_join`   |`bool`|`on` |Enables/disables to pull up tables-join if GpuJoin is just below GpuPreAgg, to reduce data transfer between CPU/RAM and GPU.|
|`pg_strom.enable_numeric_type` |`bool`|`on` |Enables/disables support of `numeric` data type in arithmetic expression on GPU device|
|`pg_strom.cpu_fallback`        |`bool`|`off`|Controls whether it actually run CPU fallback operations, if GPU program returned "CPU ReCheck Error"|
|`pg_strom.nvme_strom_enabled`  |`bool`|`on` |Enables/disables the feature of SSD-to-GPU Direct SQL Execution|
|`pg_strom.nvme_strom_threshold`|`int` |自動 |Controls the table-size threshold to invoke the feature of SSD-to-GPU Direct SQL Execution|
}

@ja{
#オプティマイザに関する設定

|パラメータ名                   |型    |初期値|説明       |
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.chunk_size`          |`int` |65534kB|PG-Stromが1回のGPUカーネル呼び出しで処理するデータブロックの大きさです。かつては変更可能でしたが、ほとんど意味がないため、現在では約64MBに固定されています。|
|`pg_strom.gpu_setup_cost`      |`real`|4000  |GPUデバイスの初期化に要するコストとして使用する値。|
|`pg_strom.gpu_dma_cost`        |`real`|10    |チャンク(64MB)あたりのDMA転送に要するコストとして使用する値。|
|`pg_strom.gpu_operator_cost`   |`real`|0.00015|GPUの演算式あたりの処理コストとして使用する値。`cpu_operator_cost`よりも大きな値を設定してしまうと、いかなるサイズのテーブルに対してもPG-Stromが選択されることはなくなる。|
}
@en{
#Optimizer Configuration

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.chunk_size`          |`int` |65534kB|Size of the data blocks processed by a single GPU kernel invocation. It was configurable, but makes less sense, so fixed to about 64MB in the current version.|
|`pg_strom.gpu_setup_cost`      |`real`|4000  |Cost value for initialization of GPU device|
|`pg_strom.gpu_dma_cost`        |`real`|10    |Cost value for DMA transfer over PCIe bus per data-chunk (64MB)|
|`pg_strom.gpu_operator_cost`   |`real`|0.00015|Cost value to process an expression formula on GPU. If larger value than `cpu_operator_cost` is configured, no chance to choose PG-Strom towards any size of tables|
}

@ja{
#エグゼキュータに関する設定

|パラメータ名                       |型    |初期値|説明       |
|:----------------------------------|:----:|:----:|:----------|
|`pg_strom.global_max_async_tasks`  |`int` |160 |PG-StromがGPU実行キューに投入する事ができる非同期タスクのシステム全体での最大値。
|`pg_strom.local_max_async_tasks`   |`int` |8   |PG-StromがGPU実行キューに投入する事ができる非同期タスクのプロセス毎の最大値。CPUパラレル処理と併用する場合、この上限値は個々のバックグラウンドワーカー毎に適用されます。したがって、バッチジョブ全体では`pg_strom.local_max_async_tasks`よりも多くの非同期タスクが実行されることになります。
|`pg_strom.max_number_of_gpucontext`|`int` |自動|GPUデバイスを抽象化した内部データ構造 GpuContext の数を指定します。通常、初期値を変更する必要はありません。
}
@en{
#Executor Configuration

|Parameter                         |Type  |Default|Description|
|:---------------------------------|:----:|:----:|:----------|
|`pg_strom.global_max_async_tasks` |`int` |160   |Number of asynchronous taks PG-Strom can throw into GPU's execution queue in the whole system.|
|`pg_strom.local_max_async_tasks`  |`int` |8     |Number of asynchronous taks PG-Strom can throw into GPU's execution queue per process. If CPU parallel is used in combination, this limitation shall be applied for each background worker. So, more than `pg_strom.local_max_async_tasks` asynchronous tasks are executed in parallel on the entire batch job.|
|`pg_strom.max_number_of_gpucontext`|`int`|auto  |Specifies the number of internal data structure `GpuContext` to abstract GPU device. Usually, no need to expand the initial value.|
}

@ja{
#列指向キャッシュ関連の設定

|パラメータ名                  |型      |初期値    |説明       |
|:-----------------------------|:------:|:---------|:----------|
|`pg_strom.ccache_base_dir`    |`string`|`'/dev/shm'`|列指向キャッシュを保持するファイルシステム上のパスを指定します。通常、`tmpfs`がマウントされている`/dev/shm`を変更する必要はありません。|
|`pg_strom.ccache_databases`   |`string`|`''`        |列指向キャッシュの非同期ビルドを行う対象データベースをカンマ区切りで指定します。`pgstrom_ccache_prewarm()`によるマニュアルでのキャッシュビルドには影響しません。|
|`pg_strom.ccache_num_builders`|`int`   |`2`       |列指向キャッシュの非同期ビルドを行うワーカープロセス数を指定します。少なくとも`pg_strom.ccache_databases`で設定するデータベースの数以上にワーカーが必要です。|
|`pg_strom.ccache_log_output`  |`bool`  |`false`   |列指向キャッシュの非同期ビルダーがログメッセージを出力するかどうかを制御します。|
|`pg_strom.ccache_total_size`  |`int`   |自動      |列指向キャッシュの上限を kB 単位で指定します。区画サイズの75%またはシステムの物理メモリの66%のいずれか小さな方がデフォルト値です。|
}
@en{
#Columnar Cache Configuration

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.ccache_base_dir`    |`string`|`'/dev/shm'`|Specifies the directory path to store columnar cache data files. Usually, no need to change from `/dev/shm` where `tmpfs` is mounted at.|
|`pg_strom.ccache_databases`   |`string`|`''`    |Specified the target databases for asynchronous columnar cache build, in comma separated list. It does not affect to the manual cache build by `pgstrom_ccache_prewarm()`.|
|`pg_strom.ccache_num_builders`|`int`   |`2`     |Specified the number of worker processes for asynchronous columnar cache build. It needs to be larger than or equeal to the number of databases in `pg_strom.ccache_databases`.|
|`pg_strom.ccache_log_output`  |`bool`  |`false` |Controls whether columnar cache builder prints log messages, or not|
|`pg_strom.ccache_total_size`  |`int`   |auto    |Upper limit of the columnar cache in kB. Default is the smaller in 75% of volume size or 66% of system physical memory.|
}

@ja{
#gstore_fdw関連の設定

|パラメータ名                   |型      |初期値    |説明       |
|:------------------------------|:------:|:---------|:----------|
|`pg_strom.gstore_max_relations`|`int`   |100       |gstore_fdwを用いた外部表数の上限です。パラメータの更新には再起動が必要です。|
}
@en{
#gstore_fdw Configuration

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.gstore_max_relations`|`int`   |100       |Upper limit of the number of foreign tables with gstore_fdw. It needs restart to update the parameter.|
}

@ja{
#GPUプログラムの生成とビルドに関連する設定

|パラメータ名                   |型      |初期値  |説明       |
|:------------------------------|:------:|:-------|:----------|
|`pg_strom.program_cache_size`  |`int`   |`256MB` |ビルド済みのGPUプログラムをキャッシュしておくための共有メモリ領域のサイズです。パラメータの更新には再起動が必要です。|
|`pg_strom.num_program_builders`|`int`|`2`|GPUプログラムを非同期ビルドするためのバックグラウンドプロセスの数を指定します。パラメータの更新には再起動が必要です。|
|`pg_strom.debug_jit_compile_options`|`bool`|`off`|GPUプログラムのJITコンパイル時に、デバッグオプション（行番号とシンボル情報）を含めるかどうかを指定します。GPUコアダンプ等を用いた複雑なバグの解析に有用ですが、性能のデグレードを引き起こすため、通常は使用すべきでありません。||`pg_strom.debug_kernel_source` |`bool`  |`off`    |このオプションが`on`の場合、`EXPLAIN VERBOSE`コマンドで自動生成されたGPUプログラムを書き出したファイルパスを出力します。|
}
@en{
#Configuration of GPU code generation and build

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.program_cache_size`  |`int` |`256MB` |Amount of the shared memory size to cache GPU programs already built. It needs restart to update the parameter.|
|`pg_strom.num_program_builders`|`int`|`2`|Number of background workers to build GPU programs asynchronously. It needs restart to update the parameter.|
|`pg_strom.debug_jit_compile_options`|`bool`|`off`|Controls to include debug option (line-numbers and symbol information) on JIT compile of GPU programs. It is valuable for complicated bug analysis using GPU core dump, however, should not be enabled on daily use because of performance degradation.|
|`pg_strom.debug_kernel_source` |`bool`  |`off`   |If enables, `EXPLAIN VERBOSE` command also prints out file paths of GPU programs written out.|
}

@ja{
#GPUデバイスに関連する設定

|パラメータ名                   |型      |初期値 |説明       |
|:------------------------------|:------:|:------|:----------|
|`pg_strom.cuda_visible_devices`|`string`|`''`   |PostgreSQLの起動時に特定のGPUデバイスだけを認識させてい場合は、カンマ区切りでGPUデバイス番号を記述します。これは環境変数`CUDA_VISIBLE_DEVICES`を設定するのと同等です。|
|`pg_strom.gpu_memory_segment_size`|`int`|`512MB`|PG-StromがGPUメモリをアロケーションする際に、1回のCUDA API呼び出しで獲得するGPUデバイスメモリのサイズを指定します。この値が大きいとAPI呼び出しのオーバーヘッドは減らせますが、デバイスメモリのロスは大きくなります。
|`pg_strom.max_num_preserved_gpu_memory`|`int`|2048|確保済みGPUデバイスメモリのセグメント数の上限を指定します。通常は初期値を変更する必要はありません。|
}
@en{
#GPU Device Configuration

|Parameter                      |Type  |Default|Description|
|:------------------------------|:----:|:----:|:----------|
|`pg_strom.cuda_visible_devices`|`string`|`''`   |List of GPU device numbers in comma separated, if you want to recognize particular GPUs on PostgreSQL startup. It is equivalent to the environment variable `CUDAVISIBLE_DEVICES`|
|`pg_strom.gpu_memory_segment_size`|`int`|`512MB`|Specifies the amount of device memory to be allocated per CUDA API call. Larger configuration will reduce the overhead of API calls, but not efficient usage of device memory.|
|`pg_strom.max_num_preserved_gpu_memory`|`int`|2048|Upper limit of the number of preserved GPU device memory segment. Usually, don't need to change from the default value.|
}
