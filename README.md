# toy-inference-engine V0
a toy inference engine inspired by nano-vllm project

the engine will contains 3 components:
1. Server
2. LLMEngine
3. Scheduler

## Server
top-level api interface provider 

func list:
1. *init(model_config, parallel_config, resource_config, scheduler_config)*: used to init inference engine
2. *generate(input_ids, sampling_params)*: used for user pass in request, execute with no barrier(assync), return with request_index for the request. the request will be passed to scheduler for further process.
3. *get_generation_result(request_index)*: used for user to get generation result from scheduler by request_index from generate func.

## Scheduler
controller for the engine

func_list:
1. *init(scheduler_config)*: init scheduler, determine the strategy to process request
2. *request_arrange(request)*: adding the request from server's func generate, arrange it with strategy init before to proper request process list
3. *compute_logits(input_ids)*: interact with LLMEngine to get next token logits for the seq
4. *post_process(logits)*: process logits with sampling params, adding result to the request list, arrange the logits with scheduler config for faster generation
5. *request_check(request_index)*: check the process state for request, if it is completed, return the corresponding completion token sequence.

## LLMEngine
a class to manage models

func_list:
1. *init(model_config, parallel_config, resource_config)*: used for init llm_engine(distributed env / model structure...)
2. *model_loader(model_path)*: loading model weights, return with a weight dict
3. *model_manager(weight_dict)*: convert the raw model weight dict to the optimized inference model structure we needed.
4. *compute_logits(input_ids)*: serve for scheduler to generate next token logits.



