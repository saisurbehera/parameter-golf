# 11L SparseMLP Baseline (1xH100 Local)

Local non-record sparse MLP baseline used to test dynamic channel-level MLP sparsity on top of the 11L XSA/EMA/Partial-RoPE/Late-QAT stack.

Config:
- 11 layers
- XSA on the last 4 layers
- EMA `0.997`
- Partial RoPE
- LN Scale
- Late QAT
- dynamic MLP channel sparsity

Results:
- final proxy `val_bpb`: `1.3607`
- submission bytes: `6450797`
- packed sparse submission estimate: `6329220`
- average MLP sparsity: `25.9115%`

W&B:
- https://wandb.ai/saisam1/parameter-golf-local/runs/uzagkhw6
