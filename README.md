# ORTCustomBeamsearchOpLibrary

### Requirements:
1. A model for internal beam search session.
2. Successful conversion of the model to custom beam search OP using steps in [Convert Script](#convert-script). 
3. 

### TBD
CustomOpApi is a wrapper around OrtAPI -> THIS IS good way to reduce the number of variables the user has to define.
Write more of these for customOp api if needed. only thing we will be passing around CustomOpApi instead of ORtAPI.


This is a custom OP to run beam search over a given model. The input to the application would be input ids as shown in the ONNX model.
However, not all the inputs are utilized?? Why do they have to be utilized?

max_length only has to be used for our case, can I make all the inputs optional except for input_ids.  This will save some time to make 
the inputs. Or make them configurable into the env.

beam_search.cc doesn't have subgraph any more, so parameters that are copied from subgraph must be initialized some way - currently, custom_beamsearch_op_library_cpu.cc has these parameters hard coded - pass this from a config file or json object that would be compiled into code to avoid delay.

AllocateBuffer uses ptr from stack and later moves to a pointer -> is this OK??


### Convert script 
Use [create_beam_search.py](create_beam_search.py) to create the custom beam search OP. It takes in the inputs needed to make the path. This doesn't validate that the model is converted, it has to run a session with the model - add this test case. 


### Issues:
1. Python bind issue. the env variable in pybind and C++ is different. When someone is trying to create an external session, and internal session both have to share the same env. This is not happening. 
2. C++ API is exposed via onnxruntime_cxx_api.h. However, custom op dll should have a pointer to OrtApi to actually call these apis. So, this has to be passed in while initializing the kernel -> Directly uses C APIs after this. 
3. Internal execution runs fine. The outputs and inputs of the onnx model are create


### Notes to compare contrib op and custom op:
1. All allocators are basically cpu_allocators, and hence are copies of ort_Allocators.
2. There are some places where ORT provides managed unique pointers: IAllocatorUniquePtr, there is some other info attached to pointer like memory info etc. These are replaced with smart pointers using std::unique_ptr<>.
3. 