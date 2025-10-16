---
show: step
version: 1.0
enable_checker: true
---

# 文生图模型调用

## 总结

- 上次 可以在纯前端 `网页里`
	- 直接 和 大模型对话 了

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262375/uid1190679-20251012-1760256122648) 

- 感觉大模型 没有人的特征
	- 如何让 大模型 扮演角色呢？？🤔

### 回到最初

```
from openai import OpenAI

client = OpenAI(
    base_url='https://api-inference.modelscope.cn/v1',
    api_key = "ms-81c1f87a-fa0a-4edc-a4a5-4bc7ba3cbbba" # ModelScope Token
)

response = client.chat.completions.create(
    model='Qwen/Qwen3-Next-80B-A3B-Instruct', # ModelScope Model-Id
    messages=[
        {
            'role': 'system',
            'content': 'You are a helpful assistant.'
        },
        {
            'role': 'user',
            'content': input("问题:")
        }
    ],
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end='', flush=True)
```

- 发给大模型的是

```
你好
```

- 结论明确

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760256749214) 

- 要求大模型扮演小猫

### 扮演小猫

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760257009127) 

```
你是一只小猫
```

- 效果

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760257125807) 

### 进一步发问

```
你是一只小猫，你爱吃什么？
```

- 模型会扮演小猫 然后说话

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760257191189) 

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760275613306) 


### 再发问

```
你有什么爱好？
```

- 没有 告诉大模型
	- 他是小猫

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760276226728) 

- 大模型 就忘了
	- 自己 是一个小猫

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760275281509) 

- 做出通用回答

### 重新设置系统人设

```
你是一只小猫，如何学习编程？
```

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760275229237) 

- 大模型再次 小猫附体

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251014-1760439452749) 

- 每次都要
	- 在对话里面 提醒大模型
	- 能不能 悄悄给大模型 设置好人设 呢？

### 原始状态

```
from openai import OpenAI

client = OpenAI(
    base_url='https://api-inference.modelscope.cn/v1',
    api_key = "ms-81c1f87a-fa0a-4edc-a4a5-4bc7ba3cbbba" # ModelScope Token
)

response = client.chat.completions.create(
    model='Qwen/Qwen3-Next-80B-A3B-Instruct', # ModelScope Model-Id
    messages=[
        {
            'role': 'system',
            'content': 'You are a helpful assistant.'
        },
        {
            'role': 'user',
            'content': input("问题:")
        }
    ],
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end='', flush=True)
```

- 大模型只会用通用的方式回答

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760278481206) 

### 修改system提示

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760277097481) 


- 可以 告诉大模型 
	- 他扮演的角色 是 `孙悟空`

```
from openai import OpenAI

client = OpenAI(
    base_url='https://api-inference.modelscope.cn/v1',
    api_key = "ms-81c1f87a-fa0a-4edc-a4a5-4bc7ba3cbbba" # ModelScope Token
)

response = client.chat.completions.create(
    model='Qwen/Qwen3-Next-80B-A3B-Instruct', # ModelScope Model-Id
    messages=[
        {
            'role': 'system',
            'content': '你是孙悟空'
        },
        {
            'role': 'user',
            'content': input("问题:")
        }
    ],
    stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end='', flush=True)
```

### 用户设定

- 都在system里面
	- 设置 人物

```
messages=[
    {
        'role': 'system',
        'content': '你是孙悟空'
    },
    {
        'role': 'user',
        'content': input("问题:")
    }
],
```

- 效果


![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760278596420) 

- 可以把message用不同颜色输出吗？

### 定义函数

```
from openai import OpenAI


def show_messages(messages):
    print("==========消息开始==============")
    counter = 1
    for message in messages:
        print("\033[4" + str(counter) + "m",end="")
        print(message,end="")
        print("\33[0m")
        counter = counter + 1
        if counter == 7:
            counter = 1
    print("==========消息结束==============")
            

client = OpenAI(
    base_url = 'https://api-inference.modelscope.cn/v1',
    api_key = 'ms-81c1f87a-fa0a-4edc-a4a5-4bc7ba3cbbba'
)
messages=[
    {
        'role': 'system',
        'content': '你是孙悟空'
    },
    {
        'role': 'user',
        'content': input("问题：")
    }
]
response = client.chat.completions.create(
    model='Qwen/Qwen3-Next-80B-A3B-Instruct', # ModelScope Model-Id
    messages=messages,
    stream=True
)

show_messages(messages)
for chunk in response:
    print(chunk.choices[0].delta.content, end='', flush=True)
```

- 系统把两个类型的消息都发给大模型
	- 大模型 得到两个消息后 进行回复

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760279128682) 



### 孙悟空

- 大模型 已经知道 自己是孙悟空

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4262417/uid1190679-20251012-1760279272958) 

- 但是他不知道在和谁说话

### 总结

- 这次 可以悄悄设置 系统人设了
	- 总共 两种 消息类型

|类型|	核心定位	|作用与特点|	典型使用场景举例|	
|---|---|---|---|
|system |	系统 | 1. 提前设定模型  <br/>2. 不直接参与对话，用户看不到<br/>3. 影响整个对话的回答风格和方向	|你是孙悟空|
|user|	对话需求发起者|1. 用户的问题<br/>2. 直接触发模型<br/>3. 来生成回应的核心<br/>|	你对取经怎么看？	|

![图片描述](https://doc.shiyanlou.com/courses/3584/labs/4245525/uid1190679-20250920-1758374355546) 

- 可以让模型知道 我是猪八戒吗？🤔
- 下次再说👋