---
title:  "[STL]MAP 활용"
excerpt: ""

categories:
  - Algorithm
tags:
  - [Algorithm, C++, Coding test, MAP]
 
date: 2022-01-23
last_modified_at: 2022-01-23


---





# [STL] MAP 활용



## 1. 몇 번 나왔는지...



- AB, BC, AB, CD...
- 위와 같은 조합들이 있다면 조합마다 몇 번 나왔는지 확인 할 수 있다.
- key 값에 알파벳 string , value값에 int.

```c++
#include<map>

using namespace std;

void solve(vector<string> a, map<stirng, int>b)
{

  for(int i = 0; i < a.size(); i++)
  {
		b[a[i]]++;
  }
  
}
```

- 벡터 a가 알파벳을 모아둔 컨테이너.
- b에 담는다.
- 주의 : AB, BA는 다르게 인식.