---
title: Version 1.9
---

### ClearML Agent 1.9.1

**New Features and Improvements**
* Add default pip version support for Python 3.12

### ClearML Agent 1.9.0

**New Features and Improvements**
* Add `NO_DOCKER` flag to `clearml-agent-services` entrypoint ([ClearML Agent GitHub PR #206](https://github.com/allegroai/clearml-agent/pull/206))
* Use `venv` module if `virtualenv` is not supported
* Find the correct python version when using a pre-installed python environment
* Add `/bin/bash` support in the task's `script.binary` property
* Add support for `.ipynb` script entry files (install nbconvert in runtime, convert file to python and execute the 
python script). Includes `CLEARML_AGENT_FORCE_TASK_INIT` patching of `.ipynb` files (post-python conversion)
* Add `CLEARML_MULTI_NODE_SINGLE_TASK` (values -1, 0, 1, 2) for easier multi-node single Task workloads
* Add default docker `agent.default_docker.match_rules` configuration option (note: `matching_rules` are ignored if `--docker container` is passed in command line)
* Add `-m module args` in script entry now supports standalone script. Standalone script is placed in a file specified 
by the `working_dir` setting in the `<dir>:<target_file>` format (e.g. `:standalone.py`), or in `untitled.py` if not specified
* Add `K8S_GLUE_POD_USE_IMAGE_ENTRYPOINT` env var to allow running k8s pods without overriding the image entrypoint 
(useful for agents using prebuilt images in k8s)
* Add venv cache mount override for non-root containers (use: `agent.docker_internal_mounts.venvs_cache`)
* Add `/bin/bash -c "command"` support. Task binary should be set to `/bin/bash` and `entry_point` should be set to `-c command`
* Add support for tasks containing only bash script or python module command
* Add support for skipping container apt installs using `CLEARML_AGENT_SKIP_CONTAINER_APT` env var in k8s

**Bug Fixes**
* Fix git fetch did not update new tags ([ClearML Agent GitHub issue #209](https://github.com/allegroai/clearml-agent/issues/209))
* Fix file mode should be optional in configuration `files` section
* Fix `-m module $env` to support parsing `$env` before launching
* Fix setting tasks that were just marked as `aborted` to `started` - only force task to `started` after dequeueing it, 
otherwise do nothing
* Fix slurm multi-node rank detection
* Fix passing only `--docker` (i.e. no default container image) when using `--dynamic-gpus` feature
* Fix logger object used even if `None`
* Fix a race condition where in rare conditions popping a task from a queue that was aborted did not set it to `started` 
before the watchdog killed it (not applicable in k8s/slurm)
* Fix multi-node support to only send `pip freeze` update, only set task as `started`, and only update task status on exit for RANK 0
* Fix do not cache venv cache if venv/python skip env var was set
* Fix use same state transition in k8s if supported by the server (instead of stopping the task before re-enqueue)
* Fix failed Task in services mode logged as `User aborted` instead of `failed`. Add Task state reason string
* Fix remove task from pending queue and set to `failed` in k8s when applying the pod template fails

### ClearML SDK 1.9.3

**Bug Fixes**
* Fix broken `Task._get_status()`, which was breaking `clearml-session` in the latest version
* Fix path substitution, making it possible to store unsubstituted URL for models ([ClearML GitHub PR #935](https://github.com/allegroai/clearml/pull/935))

### ClearML SDK 1.9.2

**New Features and Improvements**
* Support parsing queue name when providing execution queue in pipelines code ([ClearML GitHub PR #857](https://github.com/allegroai/clearml/pull/857))
* Ignore `None` values for keys in the `click` argument parser ([ClearML GitHub issue #902](https://github.com/allegroai/clearml/issues/902))
* Improve docstrings for `Task.mark_completed()` and `Task.close()` (ClearML GitHub PRs [#920](https://github.com/allegroai/clearml/pull/920) and [#921](https://github.com/allegroai/clearml/pull/921))
* Add pre/post execution callbacks to pipeline steps through `@PipelineDecorator.component`
* Add status-change callback to pipeline steps through `PipelineController.add_step()`, `PipelineController.add_function_step()`, 
and `@PipelineDecorator.component`

**Bug Fixes**
* Fix missing debug samples when reporting using TensorBoard ([ClearML GitHub issue #923](https://github.com/allegroai/clearml/issues/923))
* Fix wrong Jupyter API token during repository detection ([ClearML GitHub PR #904](https://github.com/allegroai/clearml/pull/904))
* Fix typo in the warning for very large git diffs ([ClearML GitHub PR #932](https://github.com/allegroai/clearml/pull/932))
* Fix pipelines from tasks don't propagate `parameter_override` values in `PipelineController.add_step()`
* Fix folders and files uploaded to S3 and Azure with `StorageManager.upload_file()` receive wrong MIME types
* Fix the CSV file preview in Datasets
* Fix `Task.connect_configuration()` doesn't work with non-string dictionary keys 
* Fix `lightgbm_example` deprecation warning 
* Fix potential race condition in `get_or_create_project()`


### ClearML SDK 1.9.1

**New Features and Improvements**
* Add signature version to `boto3` configuration ([ClearML GitHub issue #883](https://github.com/allegroai/clearml/issues/883))
* Allow requesting custom token expiration using the `api.auth.req_token_expiration_sec` configuration setting
* Add Python 3.11 support

**Bug Fixes**
* Fix `UniformParameterRange.to_list` throws error when step size is not defined ([ClearML GitHub issue #858](https://github.com/allegroai/clearml/issues/858))
* Fix `StorageManager.list()` does not return size metadata ([ClearML GitHub issue #865](https://github.com/allegroai/clearml/issues/865))
* Fix storage with path substitutions ([ClearML GitHub issue #825](https://github.com/allegroai/clearml/issues/825))
* Fix extras in ClearML installation prevents clearml from being included in requirements ([ClearML GitHub issue #867](https://github.com/allegroai/clearml/issues/867))
* Fix metadata set on an uploaded model object is not accessible ([ClearML GitHub issue #890](https://github.com/allegroai/clearml/issues/890))
* Fix Azure storage upload not working ([ClearML GitHub issue #868](https://github.com/allegroai/clearml/issues/868))
* Fix `task.connect` list of dicts parsed incorrectly in remote
* Fix casting `None` to `int` fails uploads and permission checks
* Fix numpy 1.24 support
* Fix `clearml-data` previews are saved on file server even when `output_uri` is specified
* Fix connecting a dictionary to task sometimes raises an exception 
* Fix authentication headers are not set on substituted fileserver URLs
* Fix `Task.get_project_id()` cannot find hidden projects
* Fix TriggerScheduler docstrings ([ClearML GitHub issue #881](https://github.com/allegroai/clearml/issues/881))


### ClearML Server 1.9.2

**Bug Fixes**
* Fix "Unable to load image" error in UI experiment debug samples after entering storage credentials ([ClearML Web GitHub issue #46](https://github.com/allegroai/clearml-web/issues/46))
* Fix incorrect default project name displayed in UI "Clone Experiment" modal ([ClearML Server GitHub issue #171](https://github.com/allegroai/clearml-server/issues/171))
* Fix UI experiment debug sample viewer iteration slider not working
* Fix UI experiment console log jumps backwards unnecessarily on refresh
* Fix UI published report is not expandable
* Fix UI published report's description is not editable
* Fix UI experiment-reset popup displays incorrect experiment count 

### ClearML Server 1.9.1

**New Features and Improvements**
* Add collapsible description panel for UI reports 
* Add in-app markdown guide for UI reports

**Bug Fixes**
* Fix can't generate new pipeline runs from UI ([ClearML Server GitHub issue #169](https://github.com/allegroai/clearml-server/issues/169))
* Fix plot legend texts overlap in UI reports
* Fix UI embedded plot colors are sometimes obscure
* Fix `ctrl z` doesn't undo codeblocks in UI reports
* Fix "Getting Started" popup wrongfully showing when enqueuing tasks

### ClearML SDK 1.9.0 

**New Features and Improvements**
* Add `r` prefix to `re.match()` strings ([ClearML GitHub issue #834](https://github.com/allegroai/clearml/issues/834))
* Add `path_substitution` to `clearml.conf` example file ([ClearML GitHub PR #842](https://github.com/allegroai/clearml/pull/842))
* Clarify `deferred_init` usage in `Task.init()` ([ClearML GitHub issue #855](https://github.com/allegroai/clearml/issues/855))
* Add pipeline decorator argument to control docker image ([ClearML GitHub issue #856](https://github.com/allegroai/clearml/issues/856))
* Add `StorageManager.set_report_upload_chunk_size()` and `StorageManager.set_report_download_chunk_size()` to set chunk 
size for upload and download
* Add `allow_archived` argument in `Task.get_tasks()`
* Support querying model metadata in `Model.query_models()`
* Add `Dataset.set_metadata()` and `Dataset.get_metadata()`
* Add `delete_from_storage` (default `True`) to `Task.delete_artifacts()`

**Bug Fixes**
* Fix jsonargparse and pytorch lightning integration broken for remote execution ([ClearML GitHub issue #403](https://github.com/allegroai/clearml/issues/403))
* Fix error when using `TaskScheduler` with `limit_execution_time` ([ClearML GitHub issue #648](https://github.com/allegroai/clearml/issues/648))
* Fix dataset not synced if the changes are only modified files ([ClearML GitHub issue #822](https://github.com/allegroai/clearml/issues/822))
* Fix `StorageHelper.delete()` does not respect path substitutions ([ClearML GitHub issue #825](https://github.com/allegroai/clearml/issues/825))
* Fix can't write more than 2 GB to a file
* Fix `StorageManager.get_file_size_bytes()` returns `ClientError` instead of `None` for invalid S3 links
* Fix Dataset lineage view is broken with multiple dataset dependencies
* Fix `tensorflow_macos` support
* Fix crash when calling `task.flush(wait_for_uploads=True)` while executing remotely
* Fix `None` values get casted to empty strings when connecting a dictionary

### ClearML Server 1.9.0

**New Features and Improvements**
* New ClearML Reports UI: Create and share rich MarkDown documents supporting embeddable online content ([ClearML GitHub issue #839](https://github.com/allegroai/clearml/issues/839))
* Add user option to not show example content in the UI ([ClearML GitHub issue #774](https://github.com/allegroai/clearml/issues/774))
* Add tag filter to UI Pipeline, Dataset, and Hyper-Dataset pages
* Extend UI experiment comparison up to 100 experiments
* Allow project default output destination S3 URLs to include dots (`.`) and hyphens (`-`)

**Bug Fixes**
* Fix long experiment names break UI experiment comparison scalar plot display ([ClearML GitHub issue #166](https://github.com/allegroai/clearml-server/issues/166))
* Fix plot legend definitions set by SDK don't override UI default settings
* Fix comparing experiments with read-only components causes error 
* Fix UI model link doesn't preserve double spaces, breaking the URI
* Fix tasks/models/datasets can't be moved to UI root project
* Fix UI tag color list doesn't display tags 
* Fix UI dashboard search returns results from team's work when "My Work" filter is enabled
* Fix UI experiment console does not display complete text when large text is reported
