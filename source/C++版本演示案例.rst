C++版本演示案例
====================================================================================

C++版本演示案例的相关文件在 *./deploy/snn-cpp* 文件夹中，包含DVS-Gesture和Jester
两个例子。

运行方法
------------------------------------------------------------------------------------

**编译**

依次执行以下命令，进行编译示例代码。

::

   mkdir ./build
   cd build
   cmake ..
   make

.. attention::

   C++版本演示案例的运行依赖C++ LynSDK库，代码地址：http://192.168.9.30/system/opensource/lynsdk-cpp

.. note::

   - 执行cmake会自行下载到 *src/thirdparty* 目录。
   - 如果环境无法访问192.168.9.30，需手动下载到 *src/thirdparty* ，再进行编译。

**运行**

依次执行以下命令，运行示例。

::

   cd bin/
   ./snn_cpp

代码说明
--------------------------------------------------------------------------------

程序入口在 *src/main.cpp* 中。

::

   cout << "------gesture------" << endl;
   void \*gesture = nullptr;
   identifier_init(&gesture, 1, 60, 2, 40, 40, 11, topk, "../gesture_input", "../label", "../SeqClif3Flif2DgItout/Net_0");
   identifier_run(gesture);
   cout << "------jester------" << endl;
   void \*jester = nullptr;
   identifier_init(&jester, 1, 16, 3, 112, 112, 27, topk, "../jester_input", "../jester_label", "../ResNetLifItout/Net_0");
   identifier_run(jester);

程序先运行DVS-Gesture数据集，之后运行Jester数据集。Identifier_init中进行类的实
例化工作，传入数据集相关的参数和模型文件等。对于DVS-Gesture数据集，传入的参数1,
60, 2, 40, 40分别为b, t, c, h, w。11为类别数，topk为(1,5)， *./gesture_input* 
路径下存放的是样本的输入，每个文件对应一个样本，./label存放的是样本的标签，
*../SeqClif3Flif2DgItout/Net_0* 路径下存放的是模型的编译生成物。对于Jester数据集，
传入参数类似，其b，t,c,h,w为1, 16, 3, 112, 112。

类实例化之后，执行 ``identifier_run`` 函数进行推理。

::

   void run() {

      Model m(modelPath);                                //调用lynSDk接口加载模型编译生成物初始化模型
      LynData sOutput(m.get_outputs_size());
      CPUData cOutput(m.get_outputs_size());
      vector<string> inputFiles;
      getFileNames(inputPath, inputFiles);

      //根据样本输入文件存放的路径获取每个输入文件
      int numInput = inputFiles.size();                  //总的样本个数
      cout << "total samples: " << numInput << endl;
      utils::CPUTimePoint start;
      vector<vector<float> > results;
      Stream s;
      for(int i = 0; i < numInput; ++i)
      {
         string inputSample = inputPath + "/" + to_string(i) + ".dat";
         
         //每个样本文件
         CPUData tsData(m.get_inputs_size()*4, t);       //每个样本的空间大小
         readSample(inputSample, dataNum, tsData);       //读取每个样本文件到内存中
         for(int ti = 0; ti < t; ++ti) //遍历该样本的每个时间拍
         {
            if(ti == 0) {
            m.reset(s);                                  //如果是第0拍，重置ddr中膜电位的值
            }
            CPUData tData = tsData.slice(ti, ti + 1);    //获取该时间拍的输入数据
            CPUData tDataUint8(m.get_inputs_size());//uint8
            uint8_t array[m.get_inputs_size()];
            for (int index = 0; index < m.get_inputs_size(); ++index) {
               float tmp = tData.at<float>(0, index);
               array[index] = static_cast<uint8_t>(tmp);
            }
            std::memcpy(tDataUint8.pointer<uint8_t>(), array, m.get_inputs_size());
            LynData sModelInData(m.get_inputs_size());
            sModelInData.write(tDataUint8);              //将输入写入apu
            m.predict(s, sModelInData, sOutput);         //推理该时间拍的输出
            s.wait();
         }
         sOutput.read(cOutput);
         //取前numClasses个数
         vector<float> tmp;
         for(int m = 0; m < numClasses; ++m) {
            tmp.push_back(cOutput.at<float>(0, m));
         }
         results.push_back(tmp);
      }
      string labelFilePath = labelPath + "/" + "label.dat";
      vector<float> label(numInput);
      readLabel(labelFilePath, numInput, label);         //获取所有样本的标签
      vector<float> acc(end(topk) - begin(topk));
      accuracy_topk(results, label, topk, acc);          //计算准确率
      cout << "task/s: " << (float) numInput ((float)(chrono::duration_cast<chrono::milliseconds>((utils::CPUTimePoint() - start)).count()) / 1000) << endl;
   }
