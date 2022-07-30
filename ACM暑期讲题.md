### ACM暑期讲题

#### 回路计数--蓝桥1462

##### 题意

从1到21之间的数，若两个数互质，则可以直接相互访问，否则不行。问：从1开始，访问过所有的数字并且只访问一遍，有多少种方法？

##### 思路

按搜索来看，复杂度为`O(21!)` ，会爆。

因此考虑采用状态压缩DP。21个点为21位，每位用0 1 代表是否访问。从初始状态开始，不断尝试能到达的楼，列举所有可能的情况，如果21位数字全部变成1，代表该种情况可行。

状态转移方程：`dp[i][j]` 表示的是从状态 i 到状态 j 的路径数量。原状态 i 在楼 j 已经访问过的情况下去尝试将楼 k 加入已访问的点，如果可以，则新的状态会以k为终点，其路径数为原来的值加上`dp[ i ][ j ]`

判断x是否已经访问过： `i>>x&1` //相当于判断第x位是否为1

答案即为   `dp[1<<21-1] `

##### ![无标题-2022-07-30-1213](/Users/huangyun/Downloads/无标题-2022-07-30-1213.png)

##### Code

```cpp
#include <bits/stdc++.h>
using namespace std;
#define ll long long
bool v[25][25]; 
ll dp[1<<21][25];
inline int gcd(int a, int b){
	return b==0 ? a : gcd(b, a%b);
}
int main(void)
{
	ll res = 0;
	//存边,注意下标 
	for(int i = 1;i <= 21; i++){
		for(int j = 1; j <= 21; j++){
		if(gcd(i, j) == 1) 
			v[i-1][j-1] = v[j-1][i-1] = true;
		else
			v[i-1][j-1] = v[j-1][i-1] = false;
	}
	}
	dp[1][0] = 1;
	for(int i = 1; i < (1<<21); i++){
	for(int j = 0; j < 21; j++){
		
		//如果当前状态中不存在楼j，跳过 
		if(!(i>>j&1)) continue;
		            
		//寻找从楼j能够到达的下一栋楼
		for(int k = 0; k < 21; k++){
			
			//楼k已经访问或者j到k无边，跳过 
			if((i>>k&1) || !v[j][k]) continue;
			
			dp[i+(1<<k)][k] += dp[i][j];
		}
	}
	}
	
	//将以i为结尾点的回路求和 
	for(int i = 0; i < 21; i++) 
		res += dp[(1<<21)-1][i];
	cout <<res;
	return 0;
} 


```

