## JSON

### 一、JSON介绍

#### 1. JSON是什么？

JSON是一种轻量级的**数据交换格式**（也叫数据序列化方式），JSON采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON成为理想的**数据交换语言**。 易于人阅读和编写，同时也易于机器解析和生成，并**有效地提升网络传输效率**。

```
-------------											  -------------
|msg_type:2 |    数据序列化  JSON/XML/protobuf  数据反序列化  |msg_type:2 |
|from: xxx  |    ------->     字节流/字符流      ------->   |from: xxx  |
|to: xxx    |    ------->                      ------->   |to: xxx    |
|msg: xxx   |											  |msg: xxx   |
-------------											  -------------
```

#### 2. 本项目用到的JSON库

本项目使用的JSON库：[JSON for Modern C++](https://github.com/nlohmann/json) 是德国大牛 nlohmann 编写的在 C++ 下使用的 JSON 库。



### 二、JSON序列化

#### 1. 准备工作

打开VS Code，连接远程主机，打开远程主机的目录JSON，创建文件夹testjson，新建cpp文件`testjson.cpp`，将json的头文件`json.hpp`拉到同目录下。

#### 2. 序列化示例1—普通数据序列化

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
using namespace std;

//json序列化示例1——普通数据序列化
void func1() {
    json js;
    //键-值
    js["msg_type"] = 2;
    js["from"] = "zhang san";
    js["to"] = "li si";
    js["msg"] = "hello, what are you doing now?";

    cout << js << endl;
}
int main() {
    func1();
    
    return 0;
}
```

结果：

```C++
{"from":"zhang san","msg":"hello, what are you doing now?","msg_type":2,"to":"li si"}
```

输出是无序的，可以理解为底层是链式哈希表。

如果要将json序列化后的数据通过网络发送出去，则需要转成字符串类型，操作如下：

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json序列化示例1
void func1() {
    json js;
    //键-值
    js["msg_type"] = 2;
    js["from"] = "zhang san";
    js["to"] = "li si";
    js["msg"] = "hello, what are you doing now?";

    string sendBuf = js.dump();  //使用js的dump方法将序列化后的数据赋给sendBuf，json数据对象 -> 序列化json字符串
    cout << sendBuf.c_str() << endl;  //网络传送的是char*，所以使用string的c_str()方法将string类型变量转换成char*变量
}
int main() {
    func1();

    return 0;
}
```

#### 2. 序列化示例2—复杂数据序列化

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json序列化示例2——复杂数据序列化
void func1() {
    json js;
    //添加数组
    js["id"] = {1, 2, 3, 4, 5};
    //添加键-值
    js["name"] = "zhang san";
    //添加对象
    js["msg"]["zhang san"] = "hello world";
    js["msg"]["li si"] = "hello china";
    //上面两句等同于下面这一句的效果
    js["msg"] = {{"zhang san", "hello world"}, {"li si", "hello china"}};
    
    cout << js << endl;
}
int main() {

    func1();

    return 0;
}
```

输出：

```c++
{"id":[1,2,3,4,5],"msg":{"li si":"hello china","zhang san":"hello world"},"name":"zhang san"}
```

#### 3. 序列化示例3—容器序列化

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json序列化示例3——容器序列化
void func1() {
    json js;

    //序列化一个vector容器
    vector<int> vec;
    vec.push_back(1);
    vec.push_back(2);
    vec.push_back(5);
    js["list"] = vec;

    //序列化一个map容器
    map<int, string> m;
    m.insert({1, "黄山"});
    m.insert({2, "华山"});
    m.insert({3, "泰山"});
    js["path"] = m;

    string sendBuf = js.dump();  //json数据对象 -> 序列化json字符串
    cout << sendBuf << endl;
}
int main() {
    func1();

    return 0;
}
```

输出：

```c++
{"list":[1,2,5],"path":[[1,"黄山"],[2,"华山"],[3,"泰山"]]}
```



### 三、JSON反序列化

当从网络接收到字符串为json格式，可以使用json库直接反序列化得到数据、对象甚至容器

#### 1. 反序列化示例1

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json反序列化示例
//模拟json数据的发送
string send() {
    json js;

    js["msg_type"] = 2;
    js["from"] = "zhang san";
    js["to"] = "li si";
    js["msg"] = "hello, what are you doing now?";

    string sendBuf = js.dump();
    return sendBuf;
}

int main() {
    //recvBuf为模拟从网络中接收到的json字符串
    string recvBuf = send();
    //json数据的反序列化：通过json::parse函数把json字符串转为json对象（看作容器，方便访问）
    json jsBuf = json::parse(recvBuf);
    //输出数据
    cout << jsBuf["msg_type"] << endl;
    cout << jsBuf["from"] << endl;
    cout << jsBuf["to"] << endl;
    cout << jsBuf["msg"] << endl;

    return 0;
}
```

输出为（保留了相应的数据类型）：

```C++
2
"zhang san"
"li si"
"hello, what are you doing now?"
```

#### 2. 反序列化示例2

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json反序列化示例
//模拟json数据的发送
string send() {
    json js;

    //添加数组
    js["id"] = {1, 2, 3, 4, 5};
    //添加键-值
    js["name"] = "zhang san";
    //添加对象
    js["msg"]["zhang san"] = "hello world";
    js["msg"]["li si"] = "hello china";
    //上面两句等同于下面这一句的效果
    js["msg"] = {{"zhang san", "hello world"}, {"li si", "hello china"}};

    string sendBuf = js.dump();
    return sendBuf;
}
int main() {
    //recvBuf为模拟从网络中接收到的json字符串
    string recvBuf = send();
    //json数据的反序列化：通过json::parse函数把json字符串转为json对象（看作容器，方便访问）
    json jsBuf = json::parse(recvBuf);
    //输出数据
    cout << jsBuf["id"] << endl;
    auto id = jsBuf["id"];
    cout << id[2] << endl;

    auto msg = jsBuf["msg"];
    cout << msg["zhang san"] << endl;
    cout << msg["li si"] << endl;

    return 0;
}
```

输出：

```C++
[1,2,3,4,5]
3
"hello world"
"hello china"
```

#### 3. 反序列化示例3

```C++
#include "json.hpp"
using json = nlohmann::json;

#include <iostream>
#include <vector>
#include <map>
#include <string>
using namespace std;

//json反序列化示例
//模拟json数据的发送
string send() {
    json js;

    //序列化一个vector容器
    vector<int> vec;
    vec.push_back(1);
    vec.push_back(2);
    vec.push_back(5);
    js["list"] = vec;

    //序列化一个map容器
    map<int, string> m;
    m.insert({1, "黄山"});
    m.insert({2, "华山"});
    m.insert({3, "泰山"});
    js["path"] = m;

    string sendBuf = js.dump();
    return sendBuf;
}
int main() {
    //recvBuf为模拟从网络中接收到的json字符串
    string recvBuf = send();
    //json数据的反序列化：通过json::parse函数把json字符串转为json对象（看作容器，方便访问）
    json jsBuf = json::parse(recvBuf);
    //输出数据
    vector<int> vec = jsBuf["list"];
    for(int& v : vec) {
        cout << v;
    }
    cout << endl;

    map<int, string> m = jsBuf["path"];
    for(auto& elem : m) {
        cout << elem.first << " " << elem.second << endl;
    }

    return 0;
}
```

输出：

```C++
125
1 黄山
2 华山
3 泰山
```

