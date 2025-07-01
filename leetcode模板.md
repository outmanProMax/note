# 二分查找

```java
public int search(int[] nums, int target) {
        int high = nums.length - 1, low = 0, mid;
		//方法1，左闭右闭
        while (low <= high){
            mid = low + (high - low) / 2;//为偶数时向下取整
            if (nums[mid] == target){
                return mid//求目标值时
            } else if(nums[mid] > target){
                high = mid - 1;
            } else{
                low = mid + 1;
            }
        }
       	return high;//求左边界时
        return low;//求右边界时
}
```

# 排序+双指针

```java
public int[] sortedSearch(int[] nums) {
    Arrays.sort(nums);//数组本身有序时不需要排序
    int right = nums.length - 1;
    int left = 0;
    while (left <= right) {
        if (left右移条件) {
            left++;
        } else if (right左移条件) {
           	right--;
        } else{
            //目标区间的触发事件
            //进行去重
        	while(right > left && nums[right] == nums[right - 1])
                right--;
            while(right > left && nums[left] == nums[left + 1])
                left++;
            right--;
            left++;
        }
    }
    return result;
}
```

# 滑动窗口

```java
public int[] findValue(int[] nums) {
    int left = 0;
    for (int right = 0; right < s.length(); right++){
        while (left左移条件)){
            //左滑触发的事件
            left++;
        }
        //右滑触发的事件
    }
}
```

# 树先中后序遍历

```java
public void preOrder(TreeNode root){
    if (root == null){
        return;
    }
    //遍历触发的事件
    preOrder(root.left);
    preOrder(root.right);
}

public void inOrder(TreeNode root){
    if (root == null){
        return;
    }
    inOrder(root.left);
    //遍历触发的事件;
    inOrder(root.right);
}

public void postrOrder(TreeNode root){
    if (root == null){
        return;
    }
    postrOrder(root.left);
    postrOrder(root.right);
    //遍历触发的事件
}
```

# 树层序遍历

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    Deque<TreeNode> deque = new LinkedList<>();
    List<List<Integer>> res = new ArrayList<>();
    List<Integer> floor = new ArrayList<>();

    if (root == null)
        return res;
    deque.offer(root);
    while(!deque.isEmpty()){
        int len = deque.size();

        while (len > 0){
            TreeNode node = deque.poll();
            //每次遍历节点触发的事件
            if (node.left != null) 
                deque.offer(node.left);
            if (node.right !=  null) 
                deque.offer(node.right);
            len--;
        }

        //每次遍历完当层触发的事件
        floor.clear();
    }

    return res;
}
```

# 回溯

```java
    List<List<Integer>> res = new LinkedList<>();
    List<Integer> path = new LinkedList<>();
    boolean[] used；
    public List<List<Integer>> permute(int[] nums) {
   		used = new boolean[nums.length];
        backtracking(nums.length, nums);

        return res;
    }

    public void backtracking(int len, int[] nums){
        if (path.size() == len){
            res.add(new Arraylist<Integer>(path));
            return;
        }

        for (int i = 0; i < len; i++){
            if (!used[i]){//用于去重
                path.add(nums[i]);
                used[i] = true;
                backtracking(nums.length, nums);
                path.remove(path.size()-1);
                used[i] = false;
            }
        }
    }
```
# 单调栈

```java
	Deque<Integer> queue = new LinkedList<>();
    int[] res = new int[nums.length];

    for (int i = 0; i < nums.length; i++){
        while (!queue.isEmpty() && nums[i] > nums[queue.peek()]){
            res[queue.peek()] = i - queue.peek();
            queue.pop();
        }
        queue.push(i);
    }
}
```

# 深度优先遍历

```java
public booelean[][] used;
public int[] xAdj = {1,0,-1,0};
public int[] yAdj = {0,1,0,-1};

public void search(int[][] grid) {
    used = new boolean[grid[0].length][grid.length];
    for (int x=0;x<grid.length;x++)
    {
        for (int y=0;y<grid[0].length;y++)
        {
            DFS(grid,x,y);
        }
    }
}

public void DFS(int[][] grid,int x, int y)
{
    if(x==-1||y==-1||x==grid.length||y==grid[0].length||used[x][y])
    {
        return;
    }
    else
    {
        used[x][y] = true;
        for(int i=0;i<4;i++)
        {
            DFS(grid,x+xAdj[i],y+yAdj[i]);
        }
    }
}
```

# 动态规划

1. 建立动态规划数组
2. 动态规划数组初始化
3. 确定递推公式
4. 确定遍历顺序
5. 判断选择有用数据

## 0-1背包问题（物品数量为1个，求满足条件的物品组合种类的相关结果）

```java
    public static void testWeightBagProblem(int[] weight, int[] value, int bagWeight){
        int wLen = weight.length;
        //定义dp数组：dp[j]表示背包容量为j时，能获得的最大价值
        int[] dp = new int[bagWeight + 1];
        //非必要，进行dp数组的初始化
        dp[0] = 1;
        //遍历顺序：先遍历物品，再遍历背包容量
        for (int i = 0; i < wLen; i++){
            for (int j = bagWeight; j >= weight[i]; j--){
                //状态更新
            }
        }
    }
```

## 完全背包问题（物品数量为无限个，求满足条件的物品组合种类的相关结果）

### 先遍历物品，再遍历背包(该方法不区分21和12)

```java
private static void testCompletePack(){
    int[] weight = {1, 3, 4};
    int[] value = {15, 20, 30};
    int bagWeight = 4;
    int[] dp = new int[bagWeight + 1];
    for (int i = 0; i < weight.length; i++){ // 遍历物品
        for (int j = weight[i]; j <= bagWeight; j++){ // 遍历背包容量
            //状态更新
        }
    }
    for (int maxValue : dp){
        System.out.println(maxValue + "   ");
    }
}
```

### 先遍历背包，再遍历物品(该方法区分21和12)

```java
private static void testCompletePackAnotherWay(){
    int[] weight = {1, 3, 4};
    int[] value = {15, 20, 30};
    int bagWeight = 4;
    int[] dp = new int[bagWeight + 1];
    for (int i = 1; i <= bagWeight; i++){ // 遍历背包容量
        for (int j = 0; j < weight.length; j++){ // 遍历物品
            if (i - weight[j] >= 0){
                //状态更新
            }
        }
    }
}
```

## 