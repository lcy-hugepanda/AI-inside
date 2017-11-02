## Viterbi algorithm

### 0. Keep in mind

1. 维特比算法并非绑定于linear CRF的解码，也不是绑定于HMM。维特比算法是一个通用的动态规划算法，只是因为linear CRF和HMM中某些问题刚好可以用为特比算法解决而已。（换句话说，维特比算法可以用于包括移动通信解码、生物信息学等各种领域）

### 1. 喜闻乐见的例子

小村的村民可能有两种隐藏的状态(hidden state)：Healthy, Fever

小村的村民有三种显式的状态(observation)：normal, dizzy, cold

作为小镇的医生，你对于村民的健康状况有一个基本的认识(start probability)：

```python
start_p = {'Healthy': 0.6, 'Fever': 0.4}    
```

同时，根据你对于感冒这种疾病的医学知识，你知道“第一天感冒的话，第二天继续感冒的几率”这样的东西(transition probability)：

```python
trans_p = {
   'Healthy' : {'Healthy': 0.7, 'Fever': 0.3},
   'Fever' : {'Healthy': 0.4, 'Fever': 0.6}
   }
# 如果第一天健康，那么第二天继续健康的概率是0.7
# 以此类推
```

最后，你作为医生，对于健康/感冒时，表现出的症状也是有了解的(emission probability)：

```python
emit_p = {
   'Healthy' : {'normal': 0.5, 'cold': 0.4, 'dizzy': 0.1},
   'Fever' : {'normal': 0.1, 'cold': 0.3, 'dizzy': 0.6}
   }
# 对于健康的村民，会有0.5概率觉得normal、0.4概率觉得cold，0.1概率觉得dizzy
# 以此类推
```

那么问题来了：村民老王告诉你，他第一天觉得normal，第二天觉得cold，第三天觉得dizzy，那么这三天里面，他的healthy/fever状态序列应该是什么样的呢？

以下给出这个问题的维特比算法详细过程：

（1）第一天的observation是normal，根据start_p和emit_p，我们可以知道：

```python
第一天healthy的概率 = 0.6 * 0.5 = 0.3 
第一天fever的概率   = 0.4 * 0.1 = 0.04
# start_p * emit_p
```

（2）第二天，需要从第一天的状态转移而来，所以需要用到trans_p和emit_p：

```python
第一天healthy，第二天healthy的概率 = 0.3 * 0.7 * 0.4 = 0.084  # day1-healty x 
```

