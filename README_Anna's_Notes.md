# Changes made to the transformers repository from huggingface (version 3.0.0)

- in order to disable the warning "The current process just got forked. Disabling parallelism to avoid deadlocks... To disable this warning, please explicitly set TOKENIZERS_PARALLELISM=(true | false)" (https://github.com/huggingface/transformers/issues/5486) I added the following lines to the *run_glue.py* script (can be found in examples/text-classification):
  - `import os`
  - `os.environ["TOKENIZERS_PARALLELISM"] = "true"`
- Added *finetuning_time_list* & *finetuning_time* (minimum of *finetuning_time_list*) inside *train()* function in *trainer.py* script (can be found in src/transformers)
  - measure time with the help of `time.time()`
  - added for-loop around fine-tuning (default: run three times) in order to receive more stable finetuning-times
- Added *inference_time_list* and *inference_time* (as minimum) inside the function *_predition_loop()* in *trainer.py* script
- Added *finetuning_time_list*, *finetuning_time*, *inference_time_list* and *inference_time* to *_log()* function in order enable logging to wandb
- Added argument *finetuning_iters* in *training_args.py* to pass number of fine-tuning iterations (default: 3)
- All changes are marked with "#added by Anna"
