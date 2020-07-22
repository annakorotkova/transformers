## Changes made to the repository:

1) Added finetuning_time_list & finetuning_time (as minimum) inside "train()" function in "trainer.py" script (can be found in src/transformers)
  a) measure time with the help of time.process_time()
  b) added for-loop around fine-tuning (default: run three times, BUT: range(0,finetune_iters+1) -> accidently run 4 times instead of 3) 
     in order to receive more stable finetuning-times
      --> Added finetuning_iters in "training_args.py" to pass number of iterations
2) Added inference_time_list & inference_time (as minimum) inside "predition_loop()" function in "trainer.py" script
 
--> Added both those times in "_log()" function in order to be able to see them in wandb

#### 3) in order to disable the warning "The current process just got forked. Disabling parallelism to avoid deadlocks... To disable this warning, please explicitly set TOKENIZERS_PARALLELISM=(true | false)" (--> used when multiprocessing; often used by dataloader https://docs.python.org/3/library/multiprocessing.html; does it make sense in my case??); https://github.com/huggingface/transformers/issues/5486
  import os
  os.environ["TOKENIZERS_PARALLELISM"] = "true"
      -> in "run_glue.py" script
      
      
------------------------

## Hyperparameter combinations per task

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
  learning_rate:
    values: [1e-5, 5e-5]
  per_device_train_batch_size:
    values: [16, 128]
  max_seq_length:
    values: [128, 256, 512]
  model_name_or_path:
    values: ["bert-base-uncased", 
    "distilbert-base-uncased",  
    "roberta-base", 
    "albert-base-v1",
    "xlnet-base-cased"]
  task_name: 
    value: WNLI
  data_dir: 
    value: /home/ubuntu/WNLI 
  output_dir: 
    value: /tmp/WNLI/
  num_train_epochs:
    value: 3
  logging_steps:
    value: 50
  weight_decay:
    value: .01
  adam_epsilon:
    value: 1e-6

(Adam epsilon set to 1e-6 -> https://www.kaggle.com/philculliton/bert-optimization)

--------------------

## time

### time vs timeit: 
(https://pythonhow.com/measure-execution-time-python-code/)
 - timeit uses time.time() for Linux \& Mac; difference: garbage collection is disabled
 - time: garbage collection not disabled
 
### time.process_time(): 
(https://www.geeksforgeeks.org/time-process_time-function-in-python/)
 - always returns the float value of time in seconds
 - return the value (in fractional seconds) of the sum of the system and user CPU time of the current process
 - does not include time elapsed during sleep
 - reference point of the returned value is undefined, so that only the difference between the results of consecutive calls is valid.

### difference between CPU and clock/wall time:
https://service.futurequest.net/index.php?/Knowledgebase/Article/View/407/0/difference-between-cpu-time-and-wall-time#:~:text=CPU\%20time\%20is\%20not\%20wall,was\%20dedicated\%20to\%20a\%20process.&text=However\%2C\%20the\%20time\%20in\%20between,counted\%20towards\%20your\%20CPU\%20time.
https://serverfault.com/questions/48455/what-are-the-differences-between-wall-clock-time-user-time-and-cpu-time
https://stackoverflow.com/questions/7335920/what-specifically-are-wall-clock-time-user-cpu-time-and-system-cpu-time-in-uni

-> my choice: User CPU time with the help of time.process_time()

### inference time
mode(input) in _prediction_loop function -> Model call function (https://huggingface.co/transformers/model_doc/bert.html)

-------------------
    
## Notes:

- try out ftp16 (less precise than ftp32, but needs much less time; only works for GPU!!)
- have a look at 'benchmark'!! (already implemented functions)
- usually you have to run a function multiple times to stablize compilation (only for inference?!) & then pick the minimum
- batch_size for XLNet (recommended): 32, 48, 128 --> differs from them of BERT ?!
- are all 40 CPU kernels being used? -> Yes (see training_args.py - 'per_device_train_batch_size' -> 'Batch size per GPU/TPU core/CPU for training')

