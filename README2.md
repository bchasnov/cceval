## Task

Create a Dagster pipeline to conduct an evaluation of CrossCodeEval using CodeT5P-220M.


## Instructions

1. Uncompress the data via `tar -xvJf data/crosscodeeval_data.tar.xz  -C data/`
2. Install dependencies via `pip install -r requirements.txt`
3. Build tree sitter via `bash build_treesitter.sh`
4. Configure accelerate via `accelerate config`
5. Run job via `dagster job execute -f eval.py -c config.yaml`

## Current status

The code is a fork of the CrossCodeEval repo.
* It is modified to use the Salesforce/CodeT5P-220M language model
* Evaluation is launched via a dagster job.


## Known issues

The code is incomplete and currently has two known bugs. 
1. Running the code on a GPU system (rented on vast.ai), the dagster job successfully runs tokenization but fails model inference
2. Running the code on a CPU system (local macbook), the dagster job succesfully runs the model inference but fails computing metric evaluations.

Possible fixes:
1. Model inference fails on GPU without an error code. I suspect it is an issue with io_manager and permissions on the Vast.ai system for I/O systems.
2. Metric evaluation fails on CPU due to failure to import the module 'eval_metric'. This is strange because eval_metric.py is in the root folder. I suspect it is an issue with 🤗accelerator.

Next steps:
1. Debug the model inference step on a local computer with a GPU.
2. Debug the eval_metric importing mechanism by removing the reliance on 🤗accelerator.

## Future improvements

* Further partition the code into dagster-friendly software-defined assets to allow for modularity and a declarative approach to data management.
* Visualize the results using a Jupyter notebook as part of the dagster pipeline.


## Error logs

Below are the error logs upon testing the code on two systems.

### Testing with GPU on vast.ai

Machine information:
* RTX 3090
* Z490 Taichi
* 11th Gen Core™ i7-11700
* running nvidia/cuda_12.0.1-devel-ubuntu20.04/ssh

The dagster job results in failure without an error code.

```
root@C.7419212:~/cceval$ dagster job execute -f eval.py -c config.yaml 
11/03/2023 23:35:01 - INFO - dagster_telemetry_logger - {"action": "_logged_execute_job_started", "client_time": "2023-11-03 23:35:01.495527", "event_id": "a8212916-d63c-4ed6-af40-5c4788c1007f", "elapsed_time": "None", "instance_id": "6f7afcd0-4080-4fcf-8693-582aeeb38e55", "metadata": {}, "python_version": "3.8.10", "dagster_version": "1.5.6", "os_desc": "Linux-5.15.0-71-generic-x86_64-with-glibc2.29", "os_platform": "Linux", "run_storage_id": "fadd4c57-6d71-4259-babc-063afaca41a0", "is_known_ci_env": false}
11/03/2023 23:35:01 - INFO - dagster_telemetry_logger - {"action": "update_repo_stats", "client_time": "2023-11-03 23:35:01.497769", "event_id": "f2b94568-0f08-45ff-a9d4-12009f49d131", "elapsed_time": "", "instance_id": "6f7afcd0-4080-4fcf-8693-582aeeb38e55", "metadata": {"source": "execute_pipeline", "pipeline_name_hash": "c058c22b2e78ed2ada49355329cdbcd1b77229b4f82f2ef87b92259f0a081174", "num_pipelines_in_repo": "1", "num_schedules_in_repo": "0", "num_sensors_in_repo": "0", "num_assets_in_repo": "0", "repo_hash": "0007c0500647eafa0ca1f09296e20bc764ce8b121ad1029c4a2d39cfce9d27d3", "num_dynamic_partitioned_assets_in_repo": "0"}, "python_version": "3.8.10", "dagster_version": "1.5.6", "os_desc": "Linux-5.15.0-71-generic-x86_64-with-glibc2.29", "os_platform": "Linux", "run_storage_id": "", "is_known_ci_env": false}
11/03/2023 23:35:01 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 23:35:01 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 23:35:01 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 23:35:01 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 23:35:01 - INFO - alembic.runtime.migration - Running stamp_revision  -> ec80dd91891a
2023-11-03 23:35:01 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - RUN_START - Started execution of run for "eval_job".
2023-11-03 23:35:02 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - ENGINE_EVENT - Executing steps using multiprocess executor: parent process (pid: 1325)
2023-11-03 23:35:02 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - eval_op - STEP_WORKER_STARTING - Launching subprocess for "eval_op".
11/03/2023 23:35:04 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 23:35:04 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 23:35:04 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 23:35:04 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 23:35:04 - INFO - alembic.runtime.migration - Running stamp_revision  -> ec80dd91891a
2023-11-03 23:35:04 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1360 - STEP_WORKER_STARTED - Executing step "eval_op" in subprocess.
2023-11-03 23:35:04 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1360 - eval_op - RESOURCE_INIT_STARTED - Starting initialization of resources [io_manager].
2023-11-03 23:35:04 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1360 - eval_op - RESOURCE_INIT_SUCCESS - Finished initialization of resources [io_manager].
11/03/2023 23:35:04 - INFO - dagster_telemetry_logger - {"action": "step_start_event", "client_time": "2023-11-03 23:35:04.892994", "event_id": "32e66f85-8402-4608-802d-5bc9b98857ee", "elapsed_time": "None", "instance_id": "6f7afcd0-4080-4fcf-8693-582aeeb38e55", "metadata": {"run_id_hash": "c16a55e724a9d946ea6a6e57c48de7bb5e7c8b87f5b69c9af8bf483dfd70d41e", "step_key_hash": "3fa37b9dc8ef2819e4e64fe920ecaee54c9b3ee929e8a553b3f7aff01e01fb44"}, "python_version": "3.8.10", "dagster_version": "1.5.6", "os_desc": "Linux-5.15.0-71-generic-x86_64-with-glibc2.29", "os_platform": "Linux", "run_storage_id": "fadd4c57-6d71-4259-babc-063afaca41a0", "is_known_ci_env": false}
2023-11-03 23:35:04 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1360 - LOGS_CAPTURED - Started capturing logs in process (pid: 1360).
2023-11-03 23:35:04 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1360 - eval_op - STEP_START - Started execution of step "eval_op".
Running tokenizer on dataset: 100%|██████████| 10/10 [00:00<00:00, 364.05 examples/s]
The argument `trust_remote_code` is to be used with Auto classes. It has no effect here and is ignored.
11/03/2023 23:35:27 - INFO - eval - total samples: 10
11/03/2023 23:35:28 - INFO - dagster_telemetry_logger - {"action": "step_failure_event", "client_time": "2023-11-03 23:35:28.036766", "event_id": "de4c966c-8844-49d7-a893-a8c9a0c12e83", "elapsed_time": "None", "instance_id": "6f7afcd0-4080-4fcf-8693-582aeeb38e55", "metadata": {"run_id_hash": "c16a55e724a9d946ea6a6e57c48de7bb5e7c8b87f5b69c9af8bf483dfd70d41e", "step_key_hash": "3fa37b9dc8ef2819e4e64fe920ecaee54c9b3ee929e8a553b3f7aff01e01fb44"}, "python_version": "3.8.10", "dagster_version": "1.5.6", "os_desc": "Linux-5.15.0-71-generic-x86_64-with-glibc2.29", "os_platform": "Linux", "run_storage_id": "fadd4c57-6d71-4259-babc-063afaca41a0", "is_known_ci_env": false}
2023-11-03 23:35:28 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - ENGINE_EVENT - Multiprocess executor: parent process exiting after 26.6s (pid: 1325)
2023-11-03 23:35:28 +0000 - dagster - ERROR - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - RUN_FAILURE - Execution of run for "eval_job" failed. Steps failed: ['eval_op'].
11/03/2023 23:35:28 - INFO - dagster_telemetry_logger - {"action": "_logged_execute_job_ended", "client_time": "2023-11-03 23:35:28.732651", "event_id": "bcf4426b-1288-4969-b6f8-a9f578d58274", "elapsed_time": "0:00:27.237124", "instance_id": "6f7afcd0-4080-4fcf-8693-582aeeb38e55", "metadata": {"success": false}, "python_version": "3.8.10", "dagster_version": "1.5.6", "os_desc": "Linux-5.15.0-71-generic-x86_64-with-glibc2.29", "os_platform": "Linux", "run_storage_id": "fadd4c57-6d71-4259-babc-063afaca41a0", "is_known_ci_env": false}
2023-11-03 23:35:28 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - eval_op - RESOURCE_INIT_STARTED - Starting initialization of resources [io_manager].
2023-11-03 23:35:28 +0000 - dagster - DEBUG - eval_job - 6d455fce-9e33-4e7c-8e88-35141d18350c - 1325 - eval_op - RESOURCE_INIT_SUCCESS - Finished initialization of resources [io_manager].
Error: Run 6d455fce-9e33-4e7c-8e88-35141d18350c resulted in failure.
```



### Testing with CPU on macbook (intel)

Machine information:
* 2.7 GHz Quad-Core Intel Core i7
* Intel Iris Plus Graphics 655 1536 MB
* MacOS 13.3


```
(.env) ben@bens-MBP cceval % dagster job execute -f eval.py -c config.yaml
/Users/ben/dev/tabbyml/cceval/.env/lib/python3.9/site-packages/fuzzywuzzy/fuzz.py:11: UserWarning: Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning
  warnings.warn('Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning')
11/03/2023 15:01:11 - INFO - dagster_telemetry_logger - {"action": "_logged_execute_job_started", "client_time": "2023-11-03 15:01:11.593413", "event_id": "26531791-b855-44f9-9507-1c7d7226e71c", "elapsed_time": "None", "instance_id": "f54f85dc-f626-4b05-9d56-f0c5861145fb", "metadata": {}, "python_version": "3.9.13", "dagster_version": "1.5.6", "os_desc": "macOS-10.16-x86_64-i386-64bit", "os_platform": "Darwin", "run_storage_id": "0e7570a8-e130-4980-a5b4-6393e2d88c19", "is_known_ci_env": false}
11/03/2023 15:01:11 - INFO - dagster_telemetry_logger - {"action": "update_repo_stats", "client_time": "2023-11-03 15:01:11.597558", "event_id": "ddd2c46c-048b-45e6-80a4-cc60462a3687", "elapsed_time": "", "instance_id": "f54f85dc-f626-4b05-9d56-f0c5861145fb", "metadata": {"source": "execute_pipeline", "pipeline_name_hash": "c058c22b2e78ed2ada49355329cdbcd1b77229b4f82f2ef87b92259f0a081174", "num_pipelines_in_repo": "1", "num_schedules_in_repo": "0", "num_sensors_in_repo": "0", "num_assets_in_repo": "0", "repo_hash": "0007c0500647eafa0ca1f09296e20bc764ce8b121ad1029c4a2d39cfce9d27d3", "num_dynamic_partitioned_assets_in_repo": "0"}, "python_version": "3.9.13", "dagster_version": "1.5.6", "os_desc": "macOS-10.16-x86_64-i386-64bit", "os_platform": "Darwin", "run_storage_id": "", "is_known_ci_env": false}
11/03/2023 15:01:11 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 15:01:11 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 15:01:11 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 15:01:11 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 15:01:11 - INFO - alembic.runtime.migration - Running stamp_revision  -> ec80dd91891a
2023-11-03 15:01:11 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20729 - RUN_START - Started execution of run for "eval_job".
2023-11-03 15:01:11 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20729 - ENGINE_EVENT - Executing steps using multiprocess executor: parent process (pid: 20729)
2023-11-03 15:01:11 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20729 - eval_op - STEP_WORKER_STARTING - Launching subprocess for "eval_op".
/Users/ben/dev/tabbyml/cceval/.env/lib/python3.9/site-packages/fuzzywuzzy/fuzz.py:11: UserWarning: Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning
  warnings.warn('Using slow pure-python SequenceMatcher. Install python-Levenshtein to remove this warning')
11/03/2023 15:01:16 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 15:01:16 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 15:01:16 - INFO - alembic.runtime.migration - Context impl SQLiteImpl.
11/03/2023 15:01:16 - INFO - alembic.runtime.migration - Will assume non-transactional DDL.
11/03/2023 15:01:16 - INFO - alembic.runtime.migration - Running stamp_revision  -> ec80dd91891a
2023-11-03 15:01:16 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20737 - STEP_WORKER_STARTED - Executing step "eval_op" in subprocess.
2023-11-03 15:01:16 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20737 - eval_op - RESOURCE_INIT_STARTED - Starting initialization of resources [io_manager].
2023-11-03 15:01:16 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20737 - eval_op - RESOURCE_INIT_SUCCESS - Finished initialization of resources [io_manager].
2023-11-03 15:01:16 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20737 - LOGS_CAPTURED - Started capturing logs in process (pid: 20737).
2023-11-03 15:01:16 -0700 - dagster - DEBUG - eval_job - 862818c8-ee53-4743-ae9b-f355968f8379 - 20737 - eval_op - STEP_START - Started execution of step "eval_op".
11/03/2023 15:01:16 - INFO - dagster_telemetry_logger - {"action": "step_start_event", "client_time": "2023-11-03 15:01:16.382273", "event_id": "cac76b0b-5aec-4760-b5f8-51e4e540666b", "elapsed_time": "None", "instance_id": "f54f85dc-f626-4b05-9d56-f0c5861145fb", "metadata": {"run_id_hash": "9b989566f4c4544dcb3c7f82f0ba7512cb169f41d392ee38a5fc9d30ee4814b5", "step_key_hash": "3fa37b9dc8ef2819e4e64fe920ecaee54c9b3ee929e8a553b3f7aff01e01fb44"}, "python_version": "3.9.13", "dagster_version": "1.5.6", "os_desc": "macOS-10.16-x86_64-i386-64bit", "os_platform": "Darwin", "run_storage_id": "0e7570a8-e130-4980-a5b4-6393e2d88c19", "is_known_ci_env": false}
Running tokenizer on dataset: 100%|██████████| 10/10 [00:00<00:00, 220.55 examples/s]
The argument `trust_remote_code` is to be used with Auto classes. It has no effect here and is ignored.
11/03/2023 15:01:19 - INFO - eval - total samples: 10
[2023-11-03 15:01:19,266] [INFO] [real_accelerator.py:158:get_accelerator] Setting ds_accelerator to mps (auto detect)
[2023-11-03 15:01:19,505] torch.distributed.elastic.multiprocessing.redirects: [WARNING] NOTE: Redirects are currently not supported in Windows or MacOs.
100%|██████████| 2/2 [49:15<00:00, 1477.84s/it]
post-processing samples ...
huggingface/tokenizers: The current process just got forked, after parallelism has already been used. Disabling parallelism to avoid deadlocks...
To disable this warning, you can either:
	- Avoid using `tokenizers` before the fork if possible
	- Explicitly set the environment variable TOKENIZERS_PARALLELISM=(true | false)
huggingface/tokenizers: The current process just got forked, after parallelism has already been used. Disabling parallelism to avoid deadlocks...
To disable this warning, you can either:
	- Avoid using `tokenizers` before the fork if possible
	- Explicitly set the environment variable TOKENIZERS_PARALLELISM=(true | false)
  0%|          | 0/10 [00:00<?, ?it/s]Process SpawnPoolWorker-1:7:
Process SpawnPoolWorker-1:1:
Process SpawnPoolWorker-1:4:
Process SpawnPoolWorker-1:6:
Process SpawnPoolWorker-1:2:
Process SpawnPoolWorker-1:5:
Process SpawnPoolWorker-1:3:
Traceback (most recent call last):
Traceback (most recent call last):
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 315, in _bootstrap
    self.run()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 108, in run
    self._target(*self._args, **self._kwargs)
Traceback (most recent call last):
Traceback (most recent call last):
Traceback (most recent call last):
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 315, in _bootstrap
    self.run()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 315, in _bootstrap
    self.run()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 315, in _bootstrap
    self.run()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 315, in _bootstrap
    self.run()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 108, in run
    self._target(*self._args, **self._kwargs)
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/process.py", line 108, in run
    self._target(*self._args, **self._kwargs)
Traceback (most recent call last):
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/pool.py", line 114, in worker
    task = get()
  File "/usr/local/anaconda3/lib/python3.9/multiprocessing/queues.py", line 367, in get
    return _ForkingPickler.loads(res)
ModuleNotFoundError: No module named 'eval_metric'

```
