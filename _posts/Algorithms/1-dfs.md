---
title:  "dfs 활용"
excerpt: ""

categories:
  - Algorithm
tags:
  - [Algorithm, C++, Coding test]
 
date: 2022-01-23
last_modified_at: 2022-01-23

---





# dfs



## 1. ABCDE 조합.

- AB, AC, AD, AE, BC, BD, BE, CD, CE, DE
- ABC, ABD, ABE, BCD, BCE, CDE
- ...

위와 같이 조합을 한다면 다음과 같이 dfs를 이용할 수 있다.

```c++
void dfs(int index, int depth, string order, string comb, vector<string>ans)
{
 	if(depth == comb.size())
  {
    ans.push_back(comb);
  }
  
  for(int i = index; i < order.size(); i++)
  {
    comb[depth] = order[i];
    
    dfs(i+1, depth+1, order, comb, ans);
  }
  
  
  
}


```

- 첫번째 dfs에서 먼저 comb[0]에 A, B, C, D, E를 한번 씩 넣는다.
- 두번째 dfs에서 A라면 comb[1] 그 아래로 B,C,D,E를 한번씩 넣는다.
- 두번째 dfs에서 B라면 comb[1] 그 아래로 C,D,E를 한번씩 넣는다.
- ...