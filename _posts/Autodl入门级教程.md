# Autodl

## 租卡、开卡

### 如何选择合适的GPU?

[不同GPU的区别](不同GPU的区别.csv)

#### 总结

1. **我要训练/微调大模型（70B参数以上）：**
    - 首选：**A100 (80G)** 或 **H800/H100**（如果预算充足）。
    - 次选：**RTX 4090 (24G)** 或 **RTX 3090 (24G)**（配合量化技术，如QLoRA）。
2. **我要跑Stable Diffusion绘图：**
    - 首选：**RTX 4090**（速度最快）。
    - 次选：**RTX 3090**（24G显存可以跑高分辨率图）。
    - 入门：**RTX 3060 (12G)**（最便宜的12G卡，性价比之王）。
3. **我要部署模型做API服务：**
    - 首选：**Tesla T4**（便宜，16G显存够跑大多数7B/13B模型）。
4. **我是学生/初学者，想跑通代码：**
    - 首选：**RTX 3060** 或 **RTX 3080**，价格低廉，足够学习。

---

## 无卡开机

![image.png](images/image.png)

“无卡模式”（No-GPU Mode）和“正常开机”（Normal Mode）的主要区别在于**是否加载显卡驱动**以及**启动后的系统环境**。

### 什么时候用“无卡模式”？

你通常不需要用它，除非遇到以下特殊情况：

#### 系统“变砖”了（驱动崩了）

- 如果你不小心卸载了显卡驱动，或者安装了一个不兼容的 CUDA 版本，导致正常开机后 `nvidia-smi` 报错，或者根本无法进入系统。
- 此时用“无卡模式”开机，因为不加载显卡，系统能顺利启动。你就可以进去重新安装正确的驱动，修复系统。

#### 不需要跑代码，只想传数据

- 有时候你只是想上传几个 G 的数据集，或者配置一下 Python 环境，不需要用到显卡算力。
- 用无卡模式开机可以节省费用（因为很多平台无卡模式只收极低的存储费或 CPU 费）。

#### 系统更新或内核升级

- 在进行大规模系统更新（如 `apt upgrade`）时，有时为了避免显卡驱动与新内核冲突，会先进入无卡模式操作，搞定后再切回正常模式。

---

## 上传代码与数据集（在无卡模式下进行）

### 方法一：直接拖拽

![image.png](images/image-1.png)

![image.png](images/image-2.png)

文件个文件夹均可通过这种方式进行（先解压缩再拖拽上传十分方便，个人喜欢这种做法）

### 方法二：点击上传箭头⬆️

![image.png](images/image-3.png)

只能上传文件（包括.zip）不能上传文件夹；故一般需要进行解压缩这一步骤：

#### 解压缩

在命令行输入 `unzip cleanrl-master.zip` 这个命令进行解压缩（一定注意路径是否正确）

![image.png](images/image-4.png)

出现这个文件夹就说明已经解压缩成功！

### 方法三：使用FileZilla上传

#### 1. 下载FileZilla

FileZilla下载地址：[https://filezilla-project.org/](https://filezilla-project.org/)

![image.png](images/image-5.png)

> 注意！**不要放在C盘，养成良好习惯！**
> 

下载完成并打开就是下面的页面：

![image.png](images/image-6.png)

#### 2. 连接远程服务器

- 打开AutoDl的“容器实例”，找到”SSH登录”

![image.png](images/image-7.png)

复制登录指令

以登录指令：`ssh -p 17885 [root@connect.westb.seetacloud.com](mailto:root@connect.westb.seetacloud.com)` 为例，其中“@”之后的东西就是“主机”，“-p”之后的一串数字就是“端口”

复制密码

- 打开FileAilla，点击左上角“文件”>>”站点管理器”>>进入之后点“新站点”

![image.png](images/image-8.png)

![image.png](images/image-9.png)

![image.png](images/image-10.png)

> 建议勾选上“总是信任该主机”，可以让你后续连接更加方便。
> 

![image.png](images/image-11.png)

显示了远程服务器的内容，说明连接成功！

#### 3. 通过拖拽的方式，将你要上传的内容拖拽到远程服务器上

- 远程服务器进入你要上传的“目的地”

比如，我要上传到 autodl-tmp（推荐上传到这里，读写速度快）

![image.png](images/image-12.png)

- 在本地文件夹中选择你要上传的“对象”

比如，我要上传已经解压过的cleanrl-master

![image.png](images/image-13.png)

- 上传成功：

![image.png](images/image-14.png)

---

### 可能遇到的问题

#### 1. 解压时遇到 “cannot find”

![image.png](images/image-15.png)

终端里的提示：`root@autodl-container-99af43bb17-5f3e07fb:~#` 中的“~”代表当前正处在系统的默认主目录下。但是我的 cleanrl-master.zip文件是上传到了 `/autodl-tmp/` 目录中。

因为终端和我上传文件的位置不在同一个文件夹里，所以当你直接输入 `unzip` 时，终端在它当前的目录（`/root`）里找不到这个文件。

- 解决方案：在命令行中输入 `cd autodl-tmp`

![image.png](images/image-16.png)

#### 2. 无法和 SFTP 服务器建立 FTP 连接，请选择合适的协议。

去检查“协议”一栏是否匹配

![image.png](images/image-17.png)

- 三种协议对比
    
    
    | 协议/平台 | 核心特点 | 主要用途 | 安全性 |
    | --- | --- | --- | --- |
    | FTP | 经典、基础的文件传输协议 | 网站维护、旧系统文件共享 | 低，明文传输 |
    | SFTP | 基于SSH的安全文件传输 | 安全的远程服务器文件管理 | 高，全程加密 |
    | Storj | 去中心化、加密的云存储平台 | 安全、私密的云端数据备份与存储 | 非常高，端到端加密 |

几乎所有现代的服务器文件传输都推荐使用 SFTP。

#### 3. 通过FileZilla上传文件，拖拽的是解压缩后的文件夹，为什么上传成了压缩包？

- 解决方案：删除原压缩包，只留下解压缩后的文件夹即可

---

## 搭建训练脚本要求环境

因为我的是网格世界（Grid World）的训练脚本，所以接下来的演示不具有普适性，环境的配置依据每个人的需求而变化。当然，你也可以用这个案例来演示一下，感受一下脚本运行。

### 1. 安装网络世界环境包

打开终端，输入命令： `pip install "gymnasium[toy-text]"`

### 2. 创建并编写训练脚本

- 在 JupyterLab 左侧的文件浏览器中，进入 `/autodl-tmp/` 目录。
- 点击顶部的 `+` 号，在 Launcher 页面选择 **Text File**，然后将文件重命名为 `train_gridworld.py`。
- 将代码放入 `train_gridworld.py` 中，保存文件（`Ctrl + S`）

```python
import gymnasium as gym
import torch
import torch.nn as nn
import torch.optim as optim
import random

# ==========================================
# 1. 超参数与环境设置
# ==========================================
# 使用非滑动的冰湖环境，这是一个标准的 4x4 网格世界
env = gym.make("FrozenLake-v1", is_slippery=False)
num_states = env.observation_space.n   # 状态空间大小：16个网格
num_actions = env.action_space.n       # 动作空间大小：4方向 (上下左右)

learning_rate = 0.1
gamma = 0.99          # 折扣因子
epsilon = 0.1         # 探索率 (epsilon-greedy)
episodes = 1000       # 训练回合数

# ==========================================
# 2. 定义 Q 网络 (近似计算 State-Action Values)
# ==========================================
class QNetwork(nn.Module):
    def __init__(self):
        super(QNetwork, self).__init__()
        # 极简网络：直接将 16 维的 One-Hot 状态映射到 4 个动作的 Q 值
        self.fc = nn.Linear(num_states, num_actions, bias=False)
        
    def forward(self, x):
        return self.fc(x)

# 将网络加载到 GPU
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
q_network = QNetwork().to(device)
optimizer = optim.SGD(q_network.parameters(), lr=learning_rate)
loss_fn = nn.MSELoss()

# 辅助函数：将离散状态转为 One-Hot 张量并放入 GPU
def get_state_tensor(state):
    tensor = torch.zeros(num_states)
    tensor[state] = 1.0
    return tensor.to(device)

# ==========================================
# 3. 训练循环 (价值迭代的神经网络化)
# ==========================================
print(f"正在使用 {device} 训练网格世界智能体...")

for episode in range(episodes):
    state, _ = env.reset()
    done = False
    total_reward = 0
    
    while not done:
        state_tensor = get_state_tensor(state)
        
        # 策略选择：基于当前的 Q 值或随机探索
        if random.uniform(0, 1) < epsilon:
            action = env.action_space.sample()
        else:
            with torch.no_grad():
                q_values = q_network(state_tensor)
                action = torch.argmax(q_values).item()
                
        # 智能体执行动作，与网格世界交互
        next_state, reward, terminated, truncated, _ = env.step(action)
        done = terminated or truncated
        
        # 计算贝尔曼目标 (Bellman Target)
        next_state_tensor = get_state_tensor(next_state)
        with torch.no_grad():
            next_q_values = q_network(next_state_tensor)
            max_next_q = torch.max(next_q_values)
            # 如果到达终点，未来价值为 0
            target_q = reward + gamma * max_next_q * (1 - int(done))
            
        # 获取当前状态-动作的 Q 值预测
        current_q_values = q_network(state_tensor)
        current_q = current_q_values[action]
        
        # 计算损失并反向传播更新网络参数
        loss = loss_fn(current_q, torch.tensor(target_q).to(device))
        
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
        
        state = next_state
        total_reward += reward
        
    # 定期打印训练进度
    if (episode + 1) % 200 == 0:
        print(f"Episode {episode + 1}/{episodes} 结束 | 最新一次回合奖励: {total_reward}")

print("训练完成！")
```

### 3. 运行脚本

回到终端，确保所在路径是 `/autoal-tmp/` 然后执行这个命令：`python train_gridworld.py` 

![image.png](images/image-18.png)

随着训练的进行，你会看到控制台打印出奖励值。当智能体找到通往目标的最短路径时，回合奖励将稳定在 `1.0`。

### 补充：添加渲染代码

- **在脚本末尾添加渲染测试代码**

请打开你的 `train_gridworld.py` 文件，滚动到最底部，将下面这段代码**追加（粘贴）到文件最后面**。这段代码会在训练结束后，新建一个开启了 `ansi` 渲染模式的环境，并使用刚刚训练好的神经网络来控制智能体走一局。

```python
import time
import os

print("\n==================================")
print("开始渲染测试：智能体寻路过程")
print("==================================")

# 1. 创建测试环境，开启 'ansi' 文本渲染模式
test_env = gym.make("FrozenLake-v1", is_slippery=False, render_mode="ansi")
state, _ = test_env.reset()
done = False

# 2. 打印初始网格状态
print(test_env.render())
time.sleep(0.5)

# 3. 开始测试循环
step_count = 0
while not done:
    state_tensor = get_state_tensor(state)
    
    # 测试阶段关闭探索（epsilon），纯贪心策略：选择 Q 值最大的动作
    with torch.no_grad():
        q_values = q_network(state_tensor)
        action = torch.argmax(q_values).item()
        
    state, reward, terminated, truncated, _ = test_env.step(action)
    done = terminated or truncated
    
    step_count += 1
    # 打印当前执行的动作及新的网格状态
    action_names = ["Left", "Down", "Right", "Up"]
    print(f"\n--- 第 {step_count} 步: 智能体选择了 {action_names[action]} ---")
    print(test_env.render())
    
    # 停顿 0.5 秒，方便你看清每一步的移动
    time.sleep(0.5)

if reward > 0:
    print("🎉 寻宝成功！神经网络完美通关！")
else:
    print("💀 掉进冰窟窿了，看来还需要更多回合的训练...")
    
test_env.close()
```

- 你会看到：

![image.png](images/image-19.png)

> 在 AutoDL 这样的云端服务器上渲染环境有一个特殊的“坑”：因为它是纯命令行的 Linux 系统，没有连接物理显示器。如果像在本地电脑上那样直接使用图形界面的 `render_mode="human"`，程序会因为找不到屏幕而报错崩溃。
> 

---

### 可能遇到的问题：

#### 1. 忘记切换输入法

![image.png](images/image-20.png)

- **取消当前输入**：现在你的终端卡在这个等待状态，请直接在键盘上按下 **`Ctrl + C`**。这会强制终止当前的输入状态，让你回到正常的带有 `#` 的命令提示符。
- **重新执行命令**：将输入法切换到纯英文状态，重新输入
