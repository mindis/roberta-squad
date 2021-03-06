# roberta-squad
roBERTa training for SQuAD 




```c

TOTAL_NUM_UPDATES=5430   # Number of training steps.
WARMUP_UPDATES=326       # Linearly increase LR over this many steps.
LR=1.5e-05               # Peak LR for fixed LR scheduler.
MAX_SENTENCES=3          # Batch size per GPU.
UPDATE_FREQ=2            # Accumulate gradients to simulate training on 8 GPUs.
DATA_DIR=qa_records_squad_q
ROBERTA_PATH=/home/paramihk/roberta.large/model.pt

CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 python3.5 ./fairseq_train.py $DATA_DIR \
    --restore-file $ROBERTA_PATH \
    --reset-optimizer --reset-dataloader --reset-meters \
    --no-epoch-checkpoints --no-last-checkpoints --no-save-optimizer-state \
    --task squad2 \
    --max-positions 512 \
    --arch roberta_qa_large \
    --dropout 0.1 --attention-dropout 0.1 --weight-decay 0.01 \
    --criterion squad2 \
    --optimizer adam --adam-betas '(0.9, 0.98)' --adam-eps 1e-06 \
    --clip-norm 0.0 \
    --lr-scheduler polynomial_decay --lr $LR \
    --fp16 --fp16-init-scale 4 --threshold-loss-scale 1 --fp16-scale-window 128 --memory-efficient-fp16 \
    --warmup-updates $WARMUP_UPDATES --total-num-update $TOTAL_NUM_UPDATES \
    --max-sentences $MAX_SENTENCES \
    --required-batch-size-multiple 1 \
    --update-freq $UPDATE_FREQ \
    --max-update $TOTAL_NUM_UPDATES \
    --ddp-backend=no_c10d \
    --num-workers=0
```
