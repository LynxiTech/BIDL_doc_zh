基于世界模型的强化学习（需要执行于灵汐类脑计算设备）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

+--------------+-------------------------------------------------------------+
| 步骤         | 执行脚本                                                    |
+==============+=============================================================+
| DreamerV2    | 在 *applications/functionalBII/worldmodel* 路径下：         |
|              |                                                             |
|              | python3 launch_infer.py \-\-configs defaults atari          |
|              | atari_pong \-\-code_adapt 1 \-\-run_apu 1 \-\-compile_apu 0 |
|              | \-\-render 0 \-\-device apu:0 \-\-checkpoint                |
|              | './Atari-Pong.pt' \-\-model analog                          |
|              |                                                             |
|              | python3 launch_infer.py \-\-configs defaults atari          |
|              | atari_pong \-\-code_adapt 1 \-\-run_apu 1 \-\-compile_apu 0 |
|              | \-\-render 0 \-\-device apu:0 \-\-checkpoint                |
|              | './Atari-Pong_spike.pt' --model spike                       |
|              |                                                             |
|              | python3 launch_infer.py \-\-configs defaults atari          |
|              | atari_breakout \-\-code_adapt 1 \-\-run_apu 1               |
|              | \-\-compile_apu 0 \-\-render 0 \-\-device apu:0             |
|              | \-\-checkpoint './Atari-Breakout.pt' \-\-model analog       |
|              |                                                             |
|              | python3 launch_infer.py \-\-configs defaults atari          |
|              | atari_spaceinvaders \-\-code_adapt 1 \-\-run_apu 1          |
|              | \-\-compile_apu 0 \-\-render 0 \-\-device apu:0             |
|              | \-\-checkpoint './Atari-Space_Invaders.pt' --model          |
|              | analog                                                      |
+--------------+-------------------------------------------------------------+