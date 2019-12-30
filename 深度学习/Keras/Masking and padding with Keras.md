## Padding sequence data
When processing sequence data, it is very common for individual samples to have different lengths.  

samples that are shorter than the longest item need to be padded with some placeholder value (alternatively, one might also truncate long samples before padding short samples).  

Keras provides an API to easily truncate and pad sequences to a common length:   
**tf.keras.preprocessing.sequence.pad_sequences**
## Masking
Now that all samples have a uniform length,  
**the model must be informed that some part of the data is actually padding and should be ignored.**  
That mechanism is ***masking***.  

There are three ways to introduce input masks in Keras models:  
> Add a **keras.layers.Masking** layer.  
Configure a **keras.layers.Embedding** layer with ***mask_zero=True***.  
Pass a mask argument manually when calling layers that support this argument (e.g. RNN layers).

## reference
[Masking and padding with Keras](https://www.tensorflow.org/guide/keras/masking_and_padding)
