Changes made:

1) Added finetuning_time inside "train()" function in "trainer.py" script (can be found in src/transformers)
2) Added inference_time inside "predition_loop()" function in "trainer.py" script
--> Added both those times in "_log()" function in order to be able to see them in wandb

Manually installed "pip install tokenizer==0.7.0" because otherwise the warning "The current process just got forked. Disabling parallelism to avoid deadlocks...
To disable this warning, please explicitly set TOKENIZERS_PARALLELISM=(true | false)" is displayed.
