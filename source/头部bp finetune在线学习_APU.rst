头部bp finetune在线学习（需执行于灵汐类脑计算设备）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+---------------+-------------------------------------------------------------+
| 应用          | 执行脚本                                                    |
+===============+=============================================================+
| DVS-Gesture到 | python3 finetune.py \-\-config dvsgesture_rgb_finetune      |
|               | \-\-checkpoint latest.pth \-\-use_lyngor 1 \-\-use_legacy 0 |
| RGB-Gesture   |                                                             |
+---------------+-------------------------------------------------------------+