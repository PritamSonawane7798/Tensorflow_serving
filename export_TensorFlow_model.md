# Serving a TensorFlow Model

## Train and export TensorFlow model
- following is a short code snippet to illustrate the general process of
saving a model to disk.
```python
export_path = './<mymodel name>/<version 1>'
print 'Exporting trained model to', export_path
builder = tf.saved_model.builder.SavedModelBuilder(export_path)
builder.add_meta_graph_and_variables(
     sess, [tf.saved_model.tag_constants.SERVING],
     signature_def_map={
          'predict_images':
              prediction_signature,
          signature_constants.DEFAULT_SERVING_SIGNATURE_DEF_KEY:
              classification_signature,
     },
     main_op=main_op)
builder.save()
```
`SavedModelBuilder.__init__` takes the following argument:

* `export_path` is the path of the export directory.
`SavedModelBuilder` will create the directory if it does not exist.

`SavedModelBuilder.add_meta_graph_and_variables()` with the following arguments:

*   `sess` is the TensorFlow session that holds the trained model you are
    exporting.

*   `signature_def_map` Signature specifies what type of model is being exported, and the
    input/output tensors to bind to when running inference.
* The special signature key `serving_default` specifies the default serving
    signature. The default serving signature def key, along with other constants
    related to signatures, are defined as part of SavedModel signature

* As an example for how `predict_signature` is defined, the util takes the
    following arguments:

    *   `inputs={'images': tensor_info_x}` specifies the input tensor info.

    *   `outputs={'scores': tensor_info_y}` specifies the scores tensor info.

    *   `method_name` is the method used for the inference.
## After exporting

Each version sub-directory contains the following files:

  * `saved_model.pb` is the serialized tensorflow::SavedModel. It includes
  one or more graph definitions of the model, as well as metadata of the
  model such as signatures.

  * `variables` are files that hold the serialized variables of the graphs.