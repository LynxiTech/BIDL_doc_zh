通用本地STDP学习（finetune学习）（需执行于GPU设备）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+--------------+-------------------------------------------------------+
| 应用         | 执行脚本                                              |
+==============+=======================================================+
| ESImagenet   | python3 finetune.py \-\-config esimagenet_finetune    |
|              | \-\-checkpoint latest.pth \-\-use_lyngor 0            |
| 到DVS-MNIST  | \-\-use_legacy 0 \-\-device cuda                      |
+--------------+-------------------------------------------------------+