- 官方文档：https://www.tensorflow.org/serving/
- 环境准备：https://www.tensorflow.org/serving/setup
- mnist 的示例：https://www.tensorflow.org/serving/serving_basic

环境准备：
```bash
sudo pip3 install grpcio

# 官方只提供了 python2 的 pip 包，下面这个安装地址是非官方的
# 安装 pip 包的作用是，可以使用 python 运行 mnist_saved_model.py 
# 而不需要使用 bazel 运行 mnist_saved_model
sudo pip3 install --upgrade tensorflow-serving-api-python3

# 添加 tensorflow-model-server 的 apt 源
echo "deb [arch=amd64] http://storage.googleapis.com/tensorflow-serving-apt stable tensorflow-model-server tensorflow-model-server-universal" | sudo tee /etc/apt/soures.list.d/tensorflow-serving.list

curl https://storage.googleapis.com/tensorflow-serving-apt/tensorflow-serving.release.pub.gpg | sudo apt-key add -

# 安装 TensorFlow ModelServer
sudo apt-get update && sudo apt-get install tensorflow-model-server
```

训练 mnist 模型，并保存, /tmp/mnist_model 为输出目录：
```bash
python3 mnist_saved_model.py /tmp/mnist_model
```

启动 tensorflow serving:
```bash
tensorflow_model_server --port=9000 --model_name=mnist --model_base_path=/tmp/mnist_model/
```

如果启动过程中出现提示缺少 `GLIBCXX_3.4.22` 和 `CXXABI_1.3.11`，则需要更新 libstdc++6：
```bash
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get upgrade libstdc++6

# 安装完以后确认含有缺少的版本
strings /usr/lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCXX
```

运行 mnist_client.py 进行测试：
```bash
python3 mnist_client.py --num_tests=1000 --server=localhost:9000
```