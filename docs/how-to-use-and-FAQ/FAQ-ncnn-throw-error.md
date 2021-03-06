### param is too old, please regenerate

Your model file is being the old format converted by an old caffe2ncnn tool.

Checkout the latest ncnn code, build it and regenerate param and model binary files, and that should work.

Make sure that your param file starts with the magic number 7767517.

you may find more info on [use-ncnn-with-alexnet](use-ncnn-with-alexnet)

### find_blob_index_by_name XYZ failed

That means ncnn couldn't find the XYZ blob in the network. 

You shall call Extractor::input()/extract() by blob name instead of layer name.

For models loaded from binary param file or external memory, you shall call Extractor::input()/extract() by the enum defined in xxx.id.h because all the visible string literals have been stripped in binary form.

This error usually happens when the input layer is not properly converted.

You shall upgrade caffe prototxt/caffemodel before converting it to ncnn. Following snnipet type shall be ok. 

```
layer {
  name: "data"
  type: "Input"
  top: "data"
  input_param { shape: { dim: 1 dim: 3 dim: 227 dim: 227 } }
}
```

you may find more info on [use-ncnn-with-alexnet](use-ncnn-with-alexnet).

### layer XYZ not exists or registered

Your network contains some operations that are not implemented in ncnn.

You may implement them as custom layer followed in [how-to-implement-custom-layer-step-by-step](how-to-implement-custom-layer-step-by-step).

Or you could simply register them as no-op if you are sure those operations make no sense.

```
class Noop : public ncnn::Layer {};
DEFINE_LAYER_CREATOR(Noop)

net.register_custom_layer("LinearRegressionOutput", Noop_layer_creator);
net.register_custom_layer("MAERegressionOutput", Noop_layer_creator);
```

### fopen XYZ.param/XYZ.bin failed

File not found or not readable. Make sure that XYZ.param/XYZ.bin is accessible.

### network graph not ready

You shall call Net::load_param() first, then Net::load_model().

This error may also happens when Net::load_param() failed, but not properly handled.

### memory not 32-bit aligned at XYZ

The pointer passed to Net::load_param() or Net::load_model() is not 32bit aligned.

In practice, the head pointer of std::vector<unsigned char> is not guaranteed to be 32bit aligned.

you can store your binary buffer in ncnn::Mat structure, its internal memory is aligned.

### Why I get so many XYZ unsupported yet errors when converting tensorflow model

~~Sorry, I decided to give up maintaining this tool after struggling quite lots time.~~
I have given up maintaining this tool!

The tensorflow model usually contains huge amount of operations which are not implemented in ncnn, and the operations are too tiny and piecemeal that makes big runtime overhead which is not suitable for deploying on devices.

### undefined reference to '__kmpc_XYZ_XYZ'

use clang for building android shared library

comment the following line in your Application.mk
```
NDK_TOOLCHAIN_VERSION := 4.9
```
