# [stereoseq-to-adata](https://github.com/myuanz/stereoseq-to-adata)

> 快速将从 stereo-seq 导出的 dataframe 转换为 adata 格式并添加合适的元信息

> Quickly convert the dataframe exported from stereo-seq to adata format and add appropriate metadata

## Installation

```
pip install stereoseq-to-adata
```

## Usage

### For single df


```python
import s2a

adata = s2a.stereo_df_to_adata(
    <your df or your df path>,
    obs_src='cell_label' # cell_label or spot_bin
    # spot_bin_size=50 # if you are using spot_bin, spot_bin_size is required
    verbose=True
)
print(adata)
```

output:
```
2025-02-18 16:58:16 - reading /mnt/inner-data/sde/total_gene_2D/macaque-20240814-cla-all/total_gene_T89_macaque_f001_2D_macaque-20240814-cla-all.parquet...
2025-02-18 16:58:32 - raw df shape: (68613121, 8)
2025-02-18 16:58:32 - df after drop non-cell expr: (19689074, 8)
2025-02-18 16:58:32 - df columns: ['gene', 'x', 'y', 'umi_count', 'rx', 'ry', 'gene_area', 'cell_label']
2025-02-18 16:58:32 - has_rxry: True
2025-02-18 16:58:32 - start mapping...
2025-02-18 16:58:33 - n_genes: 15638, n_cells: 181398
2025-02-18 16:58:33 - creating sparse matrix...
2025-02-18 16:58:34 - creating AnnData...
2025-02-18 16:58:35 - done in 18.90 seconds

AnnData object with n_obs × n_vars = 181398 × 15638
    obs: 'region_global_id'
    obsm: 'spatial', 'spatial_r'
```

### For std folder

Use Python:

```python
import s2a

adatas = s2a.process_stereo_folder(
    <your df folder>,
    save_to=<folder to save adatas>,
    obs_src='cell_label' # cell_label or spot_bin
    # spot_bin_size=50 # if you are using spot_bin, spot_bin_size is required

)
print(adatas, end='\n...\n')
print(adatas[0].obs, end='\n...\n')
print(adatas[0].uns['export_meta'], end='\n...\n')

```

output:
```
processing files: 100%|██████████████████████████████████████████| 46/46 [00:33<00:00,  1.37it/s]
[AnnData object with n_obs × n_vars = 101001 × 15579
    obs: 'region_global_id', 'region_name'
    uns: 'export_meta'
    obsm: 'spatial', 'spatial_r', AnnData object with n_obs × n_vars = 115651 × 15352
    obs: 'region_global_id', 'region_name'
    uns: 'export_meta'
    obsm: 'spatial', 'spatial_r', AnnData object with n_obs × n_vars = 197038 × 15911
    obs: 'region_global_id', 'region_name'
    uns: 'export_meta'
    obsm: 'spatial', 'spatial_r', AnnData object with n_obs × n_vars = 156175 × 15842
    obs: 'region_global_id', 'region_name'
    uns: 'export_meta'
    ...
    ...
]
---
                region_global_id region_name
T89-cell-78                  716     L-F3-l1
T89-cell-79                  716     L-F3-l1
T89-cell-80                  716     L-F3-l1
T89-cell-83                  716     L-F3-l1
T89-cell-84                  716     L-F3-l1
...                          ...         ...
T89-cell-429516              585     L-F5-l5
T89-cell-429760              585     L-F5-l5
T89-cell-429821              647     L-F5-l6
T89-cell-430276              585     L-F5-l5
T89-cell-430305              585     L-F5-l5

[101001 rows x 2 columns]
---

{'animal_id': np.int64(1),
 'cell_mask_root': '/data/sdbd/cell-mask-rechunk-by-row/macaque',
 'cell_mask_version': 'macaque-20230418-v5',
 'chip': 'T89',
 'end_time': '2024-11-06T15:21:25.127952',
 'export_parquet': np.True_,
 'export_root': '/data/sde/total_gene_2D/macaque-20241106-mq179-F1-F7',
 'export_tsv': np.False_,
 'export_version': 'macaque-20241106-mq179-F1-F7',
 'ignore_when_no_cell': np.False_,
 'ignore_when_no_region': np.False_,
 'ignored_areas': array([], dtype=float64),
 'ntp_version': 'Mq179-motor',
 'only_for_region_mapping': np.False_,
 'pid': np.int64(3070353),
 'sec_para': {'dx': np.int64(5924), 'dy': np.int64(28129)},
 'selected_areas': array([ 900,  901,  902,  722, 1030,  709,  710,  687,  711,  712,  716,
        715,  714,  686,  713, 1183, 1192, 1193, 1194, 1195,  644,  645,
        646,  585,  647,  496,  497,  498,  500,  499,  491,  492,  493,
        495,  494]),
 'selected_areas_as_rect': np.float64(-1.0),
 'skip_unselected_areas': np.True_,
 'species': 'macaque',
 'start_time': '2024-11-06T15:17:49.475968',
 'status': 'success',
 'user': 'myuan',
 'with_cell_size': np.True_}
```

Use shell:

Related parameters are the same as above

```bash
$ python -m process_stereo_folder --help

usage: process-stereo-folder [-h] [OPTIONS]

Process a folder of stereo dataframes. Folder format should be zhengmingyuan's format:
/path/to/stereo_folder/
    region-*.csv                      # region id and region name
    total_gene_{chip_a}_*.parquet     # gene expression matrix
    total_gene_{chip_a}_*.meta.json   # meta data
    ...
    total_gene_{chip_z}_*.parquet     # gene expression matrix
    total_gene_{chip_z}_*.meta.json   # meta data
    ...

save_to: Path | None
    the path to save the AnnData objects obs_add_prefix: str
    the prefix to add to the observation names verbose: bool
    whether to print debug information workers: int
    the number of workers to use

╭─ options ───────────────────────────────────────────────╮
│ -h, --help              show this help message and exit │
│ --folder PATH|STR       (required)                      │
│ --save-to {None}|PATH|STR                               │
│                         (default: None)                 │
│ --obs-add-prefix STR    (default: '{chip}-cell-')       │
│ --obs-src {cell_label,spot_bin}                         │
│                         (default: cell_label)           │
│ --spot-bin-size INT     (default: 50)                   │
│ --verbose, --no-verbose                                 │
│                         (default: False)                │
│ --workers INT           (default: 4)                    │
│ --enable-tqdm, --no-enable-tqdm                         │
│                         (default: True)                 │
╰─────────────────────────────────────────────────────────╯
```
