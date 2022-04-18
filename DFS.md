# DFS总结
## 46.全排列
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。
```
示例 ：
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```
本题写了一个标记数组use，标记在本枝之前的几轮中已经用过的数字，之后就不用了。
```cpp
class Solution {
public:
    vector<vector<int>> ans;
    vector<int>use;
    vector<int>tmp;
    void backtrace(vector<int>& nums)
    {
        if(tmp.size()==nums.size())//回溯函数先写返回条件，到达叶子节点就要返回
        {
            ans.push_back(tmp);
            return;
        }
        for(int i=0;i<nums.size();i++)
        {
            if(use[i]) continue;
            use[i]=1;
            tmp.push_back(nums[i]);
            backtrace(nums);//回溯算法要它紧紧夹住：增加回溯下层信息，回溯，去掉下层信息方便本轮的其他枝回溯
            tmp.pop_back();
            use[i]=0;
        }
    }
    vector<vector<int>> permute(vector<int>& nums) {
        int n=nums.size();
        use.assign(n,0);//前n个初始化为0
        backtrace(nums);
        return ans;

    }
};
```
## 47.全排列II
给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。

```
输入：nums = [1,1,2]
输出：
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```
因为不能重复，所以我就在上一道题基础上，在将tmp结果存入ans时增加了一个判断，如果已经有了就不放在里面，避免重复。
```cpp
if(tmp.size()==nums.size())//回溯函数先写返回条件，到达叶子节点就要返回
{
    auto it=find(ans.begin(),ans.end(),tmp);//这里用的是algorithm头文件里面的find，vector容器没有find
    if(it==ans.end())
    ans.push_back(tmp);
    return;
}
```
然而用时很长，只击败5%。因为这只是在结果处剪枝，很多分支在DFS搜索完了，花费很大时间开销都浪费了，因此考虑在回溯前剪枝。
剪枝的时候首先把nums排一下序，在回溯位置之前增加判定：当前要加到tmp里面的数是不是和前面的相同。如果不同，则不会出现重复；如果相同，但是前一个的use位为1
说明之前的层使用过这个数字，本层可以继续回溯；如果相同，但是前一个的use位为0，说明本层已经使用过这个数，只不过用完之后清零了，不应该再回溯了
```cpp
for(int i=0;i<nums.size();i++)
{
    if(use[i]) continue;
    if(i>0&&use[i-1]==0&&nums[i]==nums[i-1]) continue;//与上题比增加此步骤
    use[i]=1;
    tmp.push_back(nums[i]);
    backtrace(nums);
    tmp.pop_back();
    use[i]=0;
}
```
