# 11L SparseMLP + Late Attention Precision (1xH100 Local)

This non-record submission documents a local 1xH100 exploration built on the current strong public direction:

- 11 layers
- XSA on the last 4 layers
- EMA `0.997`
- Partial RoPE
- LN Scale
- Late QAT

The main experiment question was whether MLP channel sparsity could free enough artifact budget to be reinvested into more careful export precision for late attention layers.

## Summary

Three local runs were compared on the same shard, same 1200-step budget, same seed, and same standard final roundtrip eval path:

1. Dense baseline
2. Sparse MLP baseline
3. Sparse MLP + higher export precision in the last 3 attention layers

The late-attention-precision variant kept the same proxy validation quality as the sparse baseline, improved final exported quality versus the dense baseline, and still stayed smaller than the dense artifact.

## Main Findings

Dense baseline:
- final proxy `val_bpb`: `1.3603`
- final roundtrip `val_bpb`: `3.47154460`
- submission bytes: `6735678`

Sparse MLP baseline:
- final proxy `val_bpb`: `1.3607`
- submission bytes: `6450797`
- packed sparse submission estimate: `6329220`
- average MLP sparsity: `25.9115%`

Sparse MLP + late attention precision:
- final proxy `val_bpb`: `1.3612`
- final roundtrip `val_bpb`: `3.42788345`
- submission bytes: `6673566`
- packed sparse submission estimate: `6620259`

Interpretation:
- plain sparse MLP saved the most bytes
- reinvesting some of those bytes into later attention layers improved final exported quality
- the late-attention-precision variant still stayed below the dense artifact size while improving the final roundtrip metric

## What Changed

Relative to the sparse MLP baseline:

- sparse MLP channel masks were kept
- packed sparse export measurement was kept
- the last 3 attention layers were exported at `int7` instead of the default `int6`
- MLP export stayed aggressive (`int5`)

This is mainly an export-policy change rather than a major architecture change. The goal is to spend saved bytes where they matter more.

## W&B

Dense baseline:
- https://wandb.ai/saisam1/parameter-golf-local/runs/cmzqo2lq

Sparse MLP baseline:
- https://wandb.ai/saisam1/parameter-golf-local/runs/uzagkhw6

Sparse MLP + late attention precision:
- https://wandb.ai/saisam1/parameter-golf-local/runs/ridvkxul

## Notes

- These are local 1xH100 runs on a small-shard workflow, not leaderboard-valid 8xH100 submissions.
- The strict sliding-window final eval path was not used for the dense-vs-late-attention-precision comparison here; both were compared on the same standard final roundtrip path.
- Packed sparse export helped, but most zeroed channels were already compressing well under `zstd`, so packed export improved bytes by about `121577` in the saved sparse run rather than by the full structural reduction.
