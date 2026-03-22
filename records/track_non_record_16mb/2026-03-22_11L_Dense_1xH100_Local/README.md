# 11L Dense Baseline (1xH100 Local)

Local non-record dense baseline used for comparison against sparse MLP experiments.

Config:
- 11 layers
- XSA on the last 4 layers
- EMA `0.997`
- Partial RoPE
- LN Scale
- Late QAT
- no sparse masking

Results:
- final proxy `val_bpb`: `1.3603`
- final roundtrip `val_bpb`: `3.47154460`
- submission bytes: `6735678`

W&B:
- https://wandb.ai/saisam1/parameter-golf-local/runs/cmzqo2lq
