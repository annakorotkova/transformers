Changes made:

1) Added finetuning_time inside "train()" function in "trainer.py" script (can be found in src/transformers)
2) Added inference_time inside "predition_loop()" function in "trainer.py" script
--> Added both those times in "_log()" function in order to be able to see them in wandb

### in order to disable the warning "The current process just got forked. Disabling parallelism to avoid deadlocks... To disable this warning, please explicitly set TOKENIZERS_PARALLELISM=(true | false)"
import os
os.environ["TOKENIZERS_PARALLELISM"] = "true"
### --> in "run_glue.py" script

write_dict[key] = value.__getstate__() in script "tokenizers_utils_base.py" (otherwise RoBERTa doesn't work)
--> didn't change anything; maybe value.content instead ?!

## Hyperparameter Kombinationen pro Datensatz

1) WNLI:
  program: /home/ubuntu/transformers-1/examples/text-classification/run_glue.py
command:
  - ${env}
  - python3
  - ${program}
  - "--do_train" 
  - "--do_eval" 
  - "--evaluate_during_training" 
  - "--overwrite_output_dir"
  - ${args}
method: grid
metric:
  name: eval_acc
  goal: maximize
parameters:
  
  -- parameters to be optimized over
  
  - learning_rate:
      values: [3e-5, 4e-5, 5e-5]
  - per_device_train_batch_size:
      values: [16, 32, 64]
  - max_seq_length:
      values: [128, 256, 384, 512]
  - model_name_or_path:
      values: ["bert-base-uncased", "bert-large-uncased", 
      "distilbert-base-uncased",  
      "roberta-base", "roberta-large",
      "albert-base-v1", "albert-large-v1",
      "xlnet-base-cased"]
  
  -- fixed parameters
  
  task_name: 
    value: WNLI
  data_dir: 
    value: /home/ubuntu/WNLI 
  output_dir: 
    value: /tmp/WNLI/
  num_train_epochs:
    value: 1
  num_train_epochs:
    value: 1
  logging_steps:
    value: 50
  weight_decay:
    value: .01
    
    
### try out ftp16 (less precise than ftp32, but needs much less time; only works for GPU!!)

### have a look at 'benchmark'!! (already implemented functions)
### usually you have to run a function multiple times to stablize compilation (only for inference?!) & then pick the minimum

### batch_size for XLNet (recommended): 32, 48, 128 --> ausprobieren ?!
### Adam epsilon = 1e-6 for XLNet (& 1e-8 for BERT, etc)
