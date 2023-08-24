# LeetCode

# Hot100

## 1. 两数之和

### 解法一：暴力枚举

通过双重循环，对每一个数字x，遍历后面的数据是否有target-x

```java
public int[] twoSum(int[] nums, int target) {
    for(int i=0;i<nums.length-1;i++){
        for(int j=i+1;j<nums.length;j++){
            if(nums[i] + nums[j] == target){
                return new int[]{nums[i],nums[j]};
            }
        }
    }
    return null;
}
```

### 解法二：哈希表解决

解法一中时间复杂度比较高是因为查找target-x的复杂度较高，可以通过哈希表将复杂度将为O(1)

对于每一个x，如果没有以target-x为key的记录，就添加一个x:index的记录，这样当遍历到target-x这个数据的时候，就可以在O(1)的复杂度内找到x

```java
public int[] twoSum2(int[] nums, int target) {
    // <nums[i],i> 形式的键值对
    Map<Integer,Integer> map = new HashMap<>();

    for(int i=0;i<nums.length;i++){
        if(map.containsKey(target-nums[i])){
            return new int[]{i,map.get(target-nums[i])};
        }
        map.put(nums[i],i);
    }
    return null;
}
```



## 2. 两数相加

思路比较简单，就是模拟两个数字相加的过程，之间注意进位的处理，以及当一个链表遍历结束之后的处理。

```java
//2. 两数相加
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode newHead = new ListNode();
    ListNode p = newHead;
    //记录进位数据
    int add = 0;
    while(l1 != null || l2 != null){
        int a = l1 == null ? 0 : l1.val;
        int b = l2 == null ? 0 : l2.val;
        int temp = a + b + add;
        add = temp >= 10 ? 1 : 0;
        temp%=10;
        ListNode node = new ListNode(temp);
        p.next = node;
        p=p.next;
        l1 = l1 == null ? null : l1.next;
        l2 = l2 == null ? null : l2.next;
    }
    if(add == 1){
        p.next = new ListNode(1);
        p=p.next;
    }
    p.next = null;
    return newHead.next;
}
```



## 3. 无重复字符的最长子串

双指针，左指针指向无重复字串的最左端，右指针指向最右端。同时维护一个max字段就可以了。

但是我们怎么知道遍历到的某一个字符是不是重复的呢，这就需要使用集合Set完成。

每遍历一个字符，就尝试添加到Set中。如果加入成功，就意味着没有重复，right右移，

如果添加失败，就以为着这个字符是重复的，这是就需要移动left。

不断向右移动left，并移除对应位置的字符，知道移除了重复的字符，并将重复字符再次加入set（左侧的重复字符移除后，右侧的字符就不重复了）

```java
public int lengthOfLongestSubstring(String s) {
    char[] chars = s.toCharArray();
    int left = 0,right = 0,res = 0;
    HashSet set = new HashSet();
    while(right < chars.length){
        if(set.add(chars[right]) == false){
            while(set.add(chars[right]) == false){
                set.remove(chars[left++]);
            }
        }
        res = Math.max(res,right-left+1);
        right++;
    }

    return res;
}
```



## 4. 寻找两个正序数组的中位数

困难题不会捏



## 5. 最长回文子串

### 暴力法

双重循环，枚举所有的字串，然后判断是不是回文串

### 动态规划



### 中心拓展法

从两种边界条件出发，不断向两边拓展，看是不是回文串，同时维护最大长度以及最大回文串的起始位置

两种边界条件就是：

- 单个字符，一定是回文串
- 两个字符，两者相等的时候才认为是回文串

对于每一个位置，都通过中心拓展函数去向两端拓展判断是不是回文串，返回本次拓展得到的回文串的长度。

主函数中维护回文串长度的最大值

```java
 // 5.最长回文子串
public String longestPalindrome(String s) {
    if(s == null || s.length() == 0){
        return s;
    }
    int start = 0,maxLen = 0;
    for(int i=0;i<s.length();i++){
        int len1 = centerGrow(s,i,i);
        int len2 = centerGrow(s,i,i+1);
        int len = Math.max(len1,len2);
        if(len > maxLen){
            maxLen = len;
            start = i - (len - 1)/2;
        }
    }
    return s.substring(start,start + maxLen);
}

public int centerGrow(String s,int left,int right){
    while(left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)){
        left--;
        right++;
    }
    return right - left - 1;
}
```

## 6. N 字形变换

### 模拟变换过程

最简单就是利用二维矩阵去模拟变换过程，然后从矩阵中得到字符串

```java
public String convert(String s , int numRows){
        int len = s.length();
        if(numRows == 1){
            return s;
        }
        int numColum = len/(numRows+(numRows>2?1:0));
        int temp = len%(numRows+(numRows>2?1:0));
        numColum = numColum*(numRows-1) + ((temp<=numRows)?1:2);
        char[][] str = new char[numRows][numColum];
        for(int i=0;i<str.length;i++){
            Arrays.fill(str[i],'0');
        }
        boolean up=false;
        int colum=0, k = 0;
        while(k != len){
            if(up == false){        //下移
                int i=0;
                while(k !=len){
                    if(i == numRows-1){
                        up = true;
                        break;
                    }
                    str[i][colum] = s.charAt(k);
                    i++;
                    k++;
                }
            }else{                  //上移
                int i =  numRows-1;
                while(k != len){
                    if(i == 0){
                        up = false;
                        break;
                    }
                    str[i][colum] = s.charAt(k);
                    i--;
                    colum++;
                    k++;
                }
            }
        }

        StringBuffer stringBuffer = new StringBuffer();
        for(int i=0;i<numRows;i++){
            for(int j=0;j<numColum;j++){
                if(str[i][j] != '0'){
                    stringBuffer.append(str[i][j]);
                }
            }
        }

        return stringBuffer.toString();
    }
```



### 利用周期性

进阶一点就是利用变换的周期性，直接构造变换之后的字符串。





## 7. 整数反转

想法就是循环遍历每一位，取出最后一位，并拼接到结果中

```c
int reverse(int x){
    long n = 0;
	while (x)
	{
		n = n * 10 + x % 10;
		x /= 10;
	}
	return n > INT_MAX || n < INT_MIN ? 0 : n;

}
```



## 11. 盛最多水的容器

双指针，用来表示左右的范围。

right-left表示容器的底，min（nums[left]，nums[right]）表示容器的高，计算容量，维护最大值

然后需要移动一个指针，如果移动较大的指针，底会降低，高可能会不变，也可能会降低，所以总体来说容量一定会降低，所以选择移动较小的指针，重复操作。

```java
public int maxArea(int[] height) {
    int left = 0,right = height.length-1;
    int ans = 0;
    while(left < right){
        ans = Math.max(ans,(right - left) * Math.min(height[left],height[right]));
        if(height[left] < height[right]){
            left++;
        }else {
            right--;
        }
    }
    return ans;
}
```



## 15. 三数之和

双指针 + 排序

排序可以方便去重，在枚举每一个数字的时时候就需要保证当前数字需要和前一个数字不同，

第一重循环枚举a，同时得到了一个target = 0 - a，在后面的循环中只要找到满足b + c = target的数字即可

第二重循环先枚举b，然后对于c的查找从向左进行

```java
//15. 三数之和
public List<List<Integer>> threeSum(int[] nums) {
    int n = nums.length;
    Arrays.sort(nums);
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
    // 枚举 a
    for (int first = 0; first < n; ++first) {
        // 需要和上一次枚举的数不相同
        if (first > 0 && nums[first] == nums[first - 1]) {
            continue;
        }
        // c 对应的指针初始指向数组的最右端
        int third = n - 1;
        int target = -nums[first];
        // 枚举 b
        for (int second = first + 1; second < n; ++second) {
            // 需要和上一次枚举的数不相同
            if (second > first + 1 && nums[second] == nums[second - 1]) {
                continue;
            }
            // 需要保证 b 的指针在 c 的指针的左侧
            while (second < third && nums[second] + nums[third] > target) {
                --third;
            }
            // 如果指针重合，随着 b 后续的增加
            // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
            if (second == third) {
                break;
            }
            if (nums[second] + nums[third] == target) {
                List<Integer> list = new ArrayList<Integer>();
                list.add(nums[first]);
                list.add(nums[second]);
                list.add(nums[third]);
                ans.add(list);
            }
        }
    }
    return ans;
}
```



## 17. 电话号码的字母组合

每输入一个数字就对应了一组字符。

通过多次循环的方式，按照输入数字的顺序，每一个数字就在原有集合的基础上，补上该数字对应的所有字符。由于循环的次数不确定，所以可以改为递归的方式实现。

```java
// 17. 电话号码的字母组合
public List<String> res17 = new ArrayList<>();
public StringBuilder str17 = new StringBuilder();
public List<String> letterCombinations(String digits) {
    if(digits == null || digits.length() == 0){
        return new ArrayList();
    }
    Map<Character, char[]> map = new HashMap<>();
    map.put('2', new char[]{'a', 'b', 'c'});
    map.put('3', new char[]{'d', 'e', 'f'});
    map.put('4', new char[]{'g', 'h', 'i'});
    map.put('5', new char[]{'j', 'k', 'l'});
    map.put('6', new char[]{'m', 'n', 'o'});
    map.put('7', new char[]{'p', 'q', 'r', 's'});
    map.put('8', new char[]{'t', 'u', 'v'});
    map.put('9', new char[]{'w', 'x', 'y', 'z'});
    letterCombinationsSolution(digits.toCharArray(),0,map);
    return res17;

}

public void letterCombinationsSolution(char[] arr, int index, Map<Character, char[]> map) {
    if(index == arr.length){
        return;
    }
    char[] chars = map.get(arr[index]);
    for(char c : chars){
        str17.append(c);
        letterCombinationsSolution(arr,index+1,map);
        str17.deleteCharAt(str17.length()-1);
    }
}
```

## 18. 四数之和

```java
// 18. 四数之和
// 同样是使用双指针，与三数之和的思路类似，在三数之和中使用一个for循环枚举数字a，然后使用双指针寻找b，c
// 在四数之和中，使用两重循环枚举a，b，然后使用双指针寻找c，d，保证a+b+c+d=target;还需要注意剪枝和去重的操作
public List<List<Integer>> fourSum(int[] nums, int target) {
    Arrays.sort(nums);
    List<List<Integer>> res = new LinkedList<>();
    for (int i = 0; i < nums.length; i++) {
        // 剪枝
        if(nums[i] > 0 && nums[i] > target){
            return res;
        }
        // 去重
        if(i > 0 && nums[i] == nums[i-1]){
            continue;
        }
        for (int j = i + 1; j < nums.length; j++) {
            // 去重
            if(j > i + 1 && nums[j] == nums[j-1]){
                continue;
            }
            int left = j + 1;
            int right = nums.length - 1;
            while(left < right){
                long sum = nums[i] + nums[j] + nums[left] + nums[right];
                if(sum > target){
                    right--;
                }else if(sum < target){
                    left++;
                }else{
                    LinkedList<Integer> list = new LinkedList<>();
                    list.add(nums[i]);
                    list.add(nums[j]);
                    list.add(nums[left]);
                    list.add(nums[right]);
                    res.add(list);
                    // 去重
                    while(left < right && nums[right] == nums[right-1]){
                        right--;
                    }
                    while(left < right && nums[left] == nums[left+1]){
                        left++;
                    }
                    right--;
                    left++;
                }
            }
        }
    }
    return res;
}
```



## 19.删除链表的倒数第 N 个结点

在进行链表处理的时候，通常可以通过添加一个新的头节点，不保存任何数据，作为链表的头节点。这样可以方便链表中的处理

在本题中可以使用双指针的方式，一个指针先走N个节点，然后两个指针一起向后走。这样先走的节点到达尾部的时候慢节点正好指在倒数第N个节点

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0, head);
    ListNode first = head;
    ListNode second = dummy;
    for (int i = 0; i < n; ++i) {
        first = first.next;
    }
    while (first != null) {
        first = first.next;
        second = second.next;
    }
    second.next = second.next.next;
    ListNode ans = dummy.next;
    return ans;
}

```



## 20. 有效的括号



括号的题目通常使用栈来实现。遇到左括号就入栈，遇到右括号就出栈。

本题中出栈的时候还需要检查括号是不是匹配

```java
// 20.有效的括号
public boolean isValid(String s) {
    if(s == null || s.length() == 0){
        return true;
    }
    Map<Character,Character> map = new HashMap<>();
    map.put('(',')');
    map.put('[',']');
    map.put('{','}');
    Deque<Character> deque = new LinkedList<>();
    for(char c : s.toCharArray()){
        if(c == '(' || c == '[' || c == '{'){
            deque.push(c);
        }else{
            if(deque.isEmpty() || map.get(deque.pop()) != c){
                return false;
            }
        }
    }
    return deque.isEmpty();
}
```



## 21. 合并两个有序链表

迭代法

加一个新的头节点方便处理一些。主要思路就是每一次将链表接到较小的一个节点上。最后一个链表先结束，注意处理这种情况即可

```java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    ListNode newHead = new ListNode(0, null);
    ListNode p = newHead;
    while (list1 != null && list2 != null) {
        if (list1.val <= list2.val) {
            p.next = list1;
            list1 = list1.next;

        } else {
            p.next = list2;
            list2 = list2.next;
        }
        p = p.next;
    }
    // 有一个链表结束
    if (list1 != null) {
        p.next = list1;
    } else if (list2 != null) {
        p.next = list2;
    } else {
        p.next = null;
    }

    return newHead.next;
}
```



递归法

```java
// 21.合并两个有序链表-2
public ListNode mergeTwoLists2(ListNode list1, ListNode list2) {
    if(list1 == null){
        return list2;
    }else if(list2 == null){
        return list1;
    }else if(list1.val < list2.val){
        list1.next = mergeTwoLists2(list1.next,list2);
        return list1;
    }else {
        list2.next = mergeTwoLists2(list1,list2.next);
        return list2;
    }
}
```





## 22. 括号生成

回溯，在回溯函数中先定义出口，然后判断是不是可以添加左括号，如果可以添加左括号就继续添加，并递归回溯，删除添加的左括号。判断是否可以添加右括号，可以添加就添加并递归回溯

```java
public List<String> generateParenthesis(int n) {
    List<String> res = new ArrayList<>();
    backTrack22(res,new StringBuilder(),0,0,n);
    return res;
}

public void backTrack22(List<String> res,StringBuilder str,int open,int close,int max){
    if(str.length() == max * 2){
        res.add(str.toString());
        return;
    }
    // 还可以继续添加左括号
    if(open < max){
        str.append('(');
        backTrack22(res,str,open+1,close,max);
        // 回溯，删除添加的左括号
        str.deleteCharAt(str.length() - 1);
    }
    // 可以添加右括号
    if(close < open){
        str.append(')');
        backTrack22(res,str,open,close + 1,max);
        str.deleteCharAt(str.length() - 1);
    }
}
```





## 31. 下一个排列

下一个排列一定是第一个大于当前排列 或者 在当前排列最大的情况下会变成最小排列

如何找到下一个更大的排列呢？只需要从右向左遍历，找到第一个nums[i] < nums[i+1]，也就是左侧数字小于右侧数字的时候，我们就找到了一个考前的较小的数字。我们需要把这个数字与一个大于该数字的数交换，使排列变大。由于右侧数字从右向左是递增的，我们找到第一个大于nums[i]的数字nums[j]，交换两数即可。

比如排列52431，我们可以找到目标位置nums[i] = 2,nums[j] = 3，交换后得到53421，但是下一个排列应该是53124，所以就需要对3后面的数字反转，是的这些数字从左向右递增

```java
public void nextPermutation(int[] nums) {
    int i = nums.length - 2;
    while (i >= 0 && nums[i] >= nums[i + 1]) {
        i--;
    }
    if (i >= 0) {
        int j = nums.length - 1;
        while (j >= 0 && nums[i] >= nums[j]) {
            j--;
        }
        swap(nums, i, j);
    }
    reverse(nums, i + 1);

}

public void swap(int[] nums,int i,int j){
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

public void reverse(int[] nums, int start) {
    int left = start, right = nums.length - 1;
    while (left < right) {
        swap(nums, left, right);
        left++;
        right--;
    }
}
```







## 33. 搜索旋转排序数组

对于已经排序的数组的查找通常都可以使用二分查找实现。

对于本题，通过二分找到中间位置，那么这个中间位置的左侧或者右侧一定有一个是有序的

如果是左侧为有序数组，也就是nums[0] < nums[mid] ，就说明左侧整体是有序的（因为对于发生了旋转的数组，那么nums[0]一定大于最右边的元素）。如果不满足nums[0] < nums[mid] 就说明mid右侧的数组是有序的



对于mid左侧有序的情况，就要判断target是不是在mid左边，比较条件就是nums[0] <= target <= nums[mid]

如果满足就移动right，不满足就移动left

对于mid右侧有序的情况，就要判断target是不是在mid右边，比较条件就是

nums[mid] <= target <= nums[nums.lenght-1]，满足就移动left，不满足就移动right



对于二分查找，大体的框架很好写，就是很多的等号条件比较难判断



```java
// 33. 搜索旋转排序数组
public int search(int[] nums, int target) {
    int left = 0, right = nums.length-1;
    while(left <= right){
        int mid = (left + right) >> 1;
        // 命中就直接返回
        if(nums[mid] == target){
            return mid;
        }
        // 左侧有序
        if(nums[0] <= nums[mid]){
            // 在中点左侧部分
            if(target >= nums[0] && target <= nums[mid]){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }else{
            if(target >= nums[mid] && target <= nums[nums.length - 1]){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
    }
    return -1;
}
```



## 34. 在排序数组中查找元素的第一个和最后一个位置



有一种简单的写法就是先通过二分找到其中一个target的位置，然后从这个位置向两端拓展。这种方式的时间复杂度并不稳定，如果整个数组都是target，那么时间都就很高，达到了O(n)

所以需要通过二分查找重复元素的左边界和右边界

想法就是在nums[mid]命中target的时候，判断是不是左右边界，如果不是就继续二分，如果是就可以返回



```java
// 34.在排序数组中查找元素的第一个和最后一个位置
public int[] searchRange(int[] nums, int target){
    int[] ans = new int[]{-1,-1};
    if(nums.length == 0){
        return ans;
    }
    int left = rangeSearch(nums,0,nums.length-1,target,true);
    int right = rangeSearch(nums,0,nums.length-1,target,false);
    if(left == Integer.MIN_VALUE || right == Integer.MIN_VALUE){
        return ans;
    }
    ans[0] = left;
    ans[1] = right;
    return ans;
}
public static int rangeSearch(int[] arr,int start,int end,int target,boolean isLeft){
    if(arr.length == 0){
        return Integer.MIN_VALUE;
    }
    int left = start,right = end;
    while(left <= right){
        int mid = (right - left)/2 + left;
        // nums[mid]命中了target，还需要考虑是找左边界还是找右边界
        if(arr[mid] == target){
            if(isLeft == true && mid>0 && arr[mid-1] == arr[mid]){
                right = mid-1;
            }else if(isLeft == false && mid<end && arr[mid+1] == arr[mid]){
                left = mid+1;
            }else{
                return mid;
            }
        }else if(arr[mid] > target){
            right = mid-1;
        }else{
            left =  mid + 1;
        }
    }
    return Integer.MIN_VALUE;
}
```



## 39. 组合总和

回溯法，不过需要注意在寻找可能解的时候要注意去重

```java
// 39.组合总和
public List<List<Integer>> res39 = new LinkedList<>();
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    Arrays.sort(candidates);
    backTrackFor39(candidates,target,0,new LinkedList<>(),0);
    return res39;
}
public void backTrackFor39(int[] candidates,int target,int sum,LinkedList<Integer> path,int index){
    // 找到出口
    if(sum == target){
        res39.add(new LinkedList<>(path));
        return;
    }
    // 从当前的index开始寻找下一个位置，可以防止出现重复的序列
    for(int i=index;i< candidates.length;i++){
        if(sum+candidates[i] > target){
            // 加上当前的值已经大于target了，由于数字递增，所以后面值会更大，直接return就行了
            return;
        }
        path.addLast(candidates[i]);
        backTrackFor39(candidates,target,sum+candidates[i],path,i);
        path.removeLast();
    }
}
```



## 46. 全排列

回溯法，每一次添加一个元素，并标记该元素已经访问了。进行递归。递归结束要撤销访问标记，撤销访问，同时将元素从已有路径中移除。

在添加结果集的时候需要注意new一个LinkedList，如果不new的话，后面的操作会影响已经添加的数据，这一点还困惑了我一会！！

```java
// 46.全排列
public List<List<Integer>> res46 = new ArrayList<>();
public List<List<Integer>> permute(int[] nums) {
    backTrackFor46(nums,new boolean[nums.length],new LinkedList<>());
    System.out.println(res46);
    return res46;
}

public void backTrackFor46(int[] nums,boolean[] isVisited,LinkedList<Integer> list){
    // 一条路径
    if(list.size() == nums.length){
        //System.out.println(list);
        // 添加到结果集
        res46.add(new LinkedList(list));
        //System.out.println(res46);
        return;
    }
    for(int i=0;i<nums.length;i++){
        // 已经访问过的店就不用重复访问了
        if(isVisited[i]){
            continue;
        }
        isVisited[i] = true;
        list.addLast(nums[i]);

        backTrackFor46(nums,isVisited,list);
        list.removeLast();
        isVisited[i] =false;
    }
}
```



## 48. 旋转图像

比较简单，一看就会

```java
// 48.旋转图像
public void rotate(int[][] matrix) {
    int n = matrix.length,count = 0;
    for(int i = 0; i< n /2; i++){
        count++;
        for(int j=count-1;j<n-count;j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[n - j - 1][i];
            matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
            matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
            matrix[j][n - i - 1] = temp;

        }
    }
}
```





## 49. 字母异位词分组

数量调用api。

针对每一个单词，进行排序，以排序后的单词为键存放到Map中，用一个列表保存排序后是这个键的单词

```java
// 49.字母异位词分组
public List<List<String>> groupAnagrams(String[] strs) {
    List<List<String>> res = new ArrayList<>();
    Map<String,List<String>> map = new HashMap<>();
    for (String str : strs) {
        char[] chars = str.toCharArray();
        Arrays.sort(chars);
        String s = String.valueOf(chars).intern();
        if(!map.containsKey(s)){
            List<String> list = new ArrayList<>();
            map.put(s,list);
        }
        map.get(s).add(str);
    }
    map.values().forEach(res::add);
    return res;
}
```





## 53. 最大子数组和

我真的很烦动态规划！！

需要维护一个最大值，同时维护当前位置之前子数组的最大值pre，pre = Math.max(pre+nums[i],nums[i])

由于子数组需要保证连续性，所以pre要么等于之前的值加上当前的值，要么就等于当前值，同时去维护最大值即可

```java
// 53.最大子数组和
public int maxSubArray(int[] nums) {
    int max = nums[0];
    int pre = nums[0]>0?nums[0]:0;
    for (int i = 1; i < nums.length; i++) {
        pre = Math.max(pre+nums[i],nums[i]);
        max = Math.max(pre,max);
    }
    return max;
}
```



## 55.跳跃游戏

贪心算法。

维护一个能到达的最右边界，每遍历一个位置的元素，判断当前元素是否可达，如果可达就更新当前能走到最右边的范围，然后看这个最右值是否能到达数组右边界

```java
// 55.跳跃游戏
public boolean canJump(int[] nums) {
    int right = 0;
    for (int i = 0; i < nums.length; i++) {
        if(i <= right){
            right = Math.max(right,i + nums[i]);
            if(right >= nums.length - 1){
                return true;
            }
        }
    }
    return false;
}
```





## 56.合并区间

简单模拟即可

```java
// 56.合并区间
public int[][] merge(int[][] intervals) {
    if(intervals.length == 0){
        return new int[0][2];
    }
    Arrays.sort(intervals,(o1,o2)->{return o1[0]-o2[0];});
    List<int[]> merged = new ArrayList<>();
    for(int i=0;i<intervals.length;i++){
        int l = intervals[i][0], r = intervals[i][1];
        // 如果结果集为空  或者不存在重叠的情况
        if(merged.size() == 0 || merged.get(merged.size() - 1)[1] < l){
            merged.add(new int[]{l,r});
        }else {
            // 更新结果集中最后一个元素的右边界
            merged.get(merged.size() - 1)[1] = Math.max(r,merged.get(merged.size()-1)[1]);
        }
    }
    return merged.toArray(new int[merged.size()][]);
}
```





## 62.不同路径

简单的二维dp。是我能做出来的为数不多的dp了

```java
// 62.不同路径
public int uniquePaths(int m, int n) {
    int[][] dp = new int[m][n];
    for (int i = 0; i < m; i++) {
        dp[i][0] = 1;
    }
    for (int i = 0; i < n; i++) {
        dp[0][n] = 1;
    }
    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    return dp[m-1][n-1];

}
```



## 64.简单路径和

又是一题简单的二维dp

```java
// 64. 最小路径和
public int minPathSum(int[][] grid) {
    int m = grid.length, n = grid[0].length;
    int[][] dp = new int[m][n];
    //dp[0][0] = grid[0][0];
    int sum = 0;
    for(int i=0;i<m;i++){
        sum+=grid[i][0];
        dp[i][0] = sum;
    }
    sum = 0;
    for(int i=0;i<n;i++){
        sum+=grid[0][i];
        dp[0][i] = sum;
    }

    for (int i = 1; i < m; i++) {
        for (int j = 1; j < n; j++) {
            dp[i][j] = grid[i][j] + Math.min(dp[i-1][j],dp[i][j-1]);
        }
    }
    return dp[m-1][n-1];
   
}
```



## 70.爬楼梯

dp

```java
//70. 爬楼梯
public int climbStairs(int n) {
    //dp[i]表示第i级台阶有多少种方法
    int[] dp = new int[n+1];
    dp[0] = 1;
    dp[1] = 1;
    for(int i=2;i<=n;i++){
        dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
}
```



## 75. 颜色分类

双指针，p0指向0的右边界，p1指向1的右边界

```java
// 75. 颜色分类
public void sortColors(int[] nums) {
    int p0 = 0, p1 = 0;
    for (int i = 0; i < nums.length; i++) {
        if(nums[i] == 1){
            int temp = nums[i];
            nums[i] = nums[p1];
            nums[p1] = temp;
            p1++;
        }else if(nums[i] == 0){
            int temp = nums[i];
            nums[i] = nums[p0];
            nums[p0] = temp;
            if(p0 < p1){
                temp = nums[i];
                nums[i] = nums[p1];
                nums[p1] = temp;
            }
            p0++;
            p1++;
        }
    }
}
```





## 78.子集

每遍历一个数字，就取出结果集中所有的子集，然后new一个新的，并add这个数字，之后再加入结果集中

```java
// 78. 子集
public List<List<Integer>> res78 = new ArrayList<>();

public List<List<Integer>> subsets(int[] nums) {
    res78.add(new ArrayList<>());
    backTrackFor78(nums);
    return res78;
}
public void backTrackFor78(int[] nums){
    for (int i = 0; i < nums.length; i++) {
        int size = res78.size();
        for (int j = 0; j < size; j++) {
            List<Integer> list = new ArrayList<>(res78.get(j));
            System.out.println(list);
            list.add(nums[i]);
            res78.add(list);
        }
    }
}
```



## 79.单词搜索

在外层有一个双重循环，寻找第一次命中字符的位置，然后从该位置开始向四周（上、下、左、右）搜索，直到搜索完整个字符串结束。

由于同一个字符不可重复使用，所以需要使用一个visited数组，同时需要进行回溯处理

```java
// 79. 单词搜索
// 移动的方向 上、右、下、左
public int[][] move = new int[][]{{-1,0},{0,1},{1,0},{0,-1}};
public boolean exist(char[][] board, String word) {
    //return check(board,0,0,word.toCharArray(),0,new boolean[board.length][board[0].length]);
    char[] wordArray = word.toCharArray();
    boolean[][] visited = new boolean[board.length][board[0].length];
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            // 命中第一个位置
            if(board[i][j] == wordArray[0]){
                visited[i][j] = true;
                // 对该位置进行搜索
                if(dfsFor79(board,i,j,wordArray,1,visited)){
                    System.out.println(i + " " + j + " " + 0);
                    return true;
                }else{
                    visited[i][j] = false;
                }

            }
        }
    }
    return false;
}

public boolean isPosValid(int i,int j,int maxI,int maxJ){
    return i>=0 && i < maxI && j >= 0 && j < maxJ;
}

public boolean dfsFor79(char[][] board,int indexI,int indexJ,char[] word,int indexStr,boolean[][] visited){
    if(indexStr == word.length){
        return true;
    }

    for (int k = 0; k < move.length; k++) {
        int newI = indexI + move[k][0], newJ = indexJ + move[k][1];
        if(isPosValid(newI,newJ,board.length,board[0].length) && !visited[newI][newJ] && board[newI][newJ] == word[indexStr]){
            visited[newI][newJ] = true;
            if(dfsFor79(board,newI,newJ,word,indexStr+1,visited)){
                System.out.println(newI + " " + newJ + " " + indexStr);
                return true;
            }else{
                visited[newI][newJ] = false;
            }
        }
    }

    return false;
}
```





## 94.二叉树的中序遍历

迭代方式实现。利用栈

```java
public List<Integer> inorderTraversal(TreeNode root) {
    if(root == null){
        return new ArrayList();
    }
    List<Integer> res = new ArrayList<>();
    Deque<TreeNode> deque = new LinkedList<>();
    TreeNode p = root;
    while(p!=null || !deque.isEmpty()){
        if(p != null){
            deque.push(p);
            p=p.left;
        }else{
            //System.out.println(deque);
            TreeNode pop = deque.pop();
            res.add(pop.val);
            p = pop.right;
        }
    }
    return res;
}
```



## 96. 不同的二叉搜索树

```java
// 96. 不同的二叉搜索树
public int numTrees(int n) {
    int[] dp = new int[n+1];
    dp[0] = 1;
    dp[1] = 1;
    //dp[2] = 2;
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j <= i; j++) {
            //对于第i个节点，需要考虑10000000作为根节点直到i作为根节点的情况，所以需要累加
            //一共i个节点，对于根节点j时,左子树的节点个数为j-1，右子树的节点个数为i-j
            dp[i] += dp[j - 1] * dp[i - j];
        }
    }
    return dp[n];
}
```



## 98. 验证二叉搜索树

使用递归的方式验证二叉搜索树。针对每一个节点，只需要保证这个节点的val在一个给定的范围内即可。

初始节点需要大于Long.MIN_VALUE，小于Long.MAX_VALUE,后续的左节点需要大于Long.MIN_VALUE，小于node.val，右节点需要的大于node.val，小于Long.MAX_VALUE



当然也可以使用非递归方式的中序遍历来比较。

```java
// 98. 验证二叉搜索树
public boolean isValidBST(TreeNode root) {
    return isValidBST(root,Long.MIN_VALUE, Long.MAX_VALUE);
}

public boolean isValidBST(TreeNode node,long min, long max){
    if(node == null){
        return true;
    }
    if(node.val <= min || node.val >= max){
        return false;
    }
    return isValidBST(node.left,min,node.val) && isValidBST(node.right, node.val, max);
}
```



## 101.对称二叉树

递归判断是否对称

```java
// 101. 对称二叉树
public boolean isSymmetric(TreeNode root) {
    return helperIsSymmetric(root.left,root.right);
}
public boolean helperIsSymmetric(TreeNode left,TreeNode right){
    // 都为null
    if(left == null && right == null){
        return true;
    }
    // 有一个不为null
    if(left == null || right == null){
        return false;
    }
    // 是对称二叉树的条件是两个点的val相等同时两个点的子节点也对称
    return left.val == right.val && helperIsSymmetric(left.left,right.right) && helperIsSymmetric(left.right,right.left);
}
```



## 102.二叉树的层序遍历

使用队列实现。先将根节点入队。然后不断循环，每一次取出队列中已有的元素，同时将出队的元素的子节点入队，直到队列为空结束

```java
// 102. 二叉树的层序遍历
public List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> res = new ArrayList<>();
    if(root == null){
        return res;
    }
    Deque<TreeNode> deque = new LinkedList<>();
    deque.addLast(root);
    //TreeNode p = root;
    while(!deque.isEmpty()){
        List<Integer> list = new ArrayList<>();
        int size = deque.size();
        for (int i = 0; i < size; i++) {
            TreeNode p = deque.removeFirst();
            list.add(p.val);
            if(p.left != null){
                deque.addLast(p.left);
            }
            if(p.right != null){
                deque.addLast(p.right);
            }


        }
        res.add(list);
    }
    return res;
}
```



## 104. 二叉树的最大深度

```java
// 104. 二叉树的最大深度
public int maxDepth(TreeNode root) {
    if(root == null){
        return 0;
    }
    return 1 + Math.max(maxDepth(root.left),maxDepth(root.right));
}
```



## 105. 从前序与中序遍历序列构造二叉树

从前序序列中找到根节点，然后将根节点在中序序列中的位置将中序划分为左子树和右子树。分别对左子树和右子树执行同样的操作。注意每一个子树在前序序列的开始位置和中序序列的开始位置，以及子树的长度。当子树长度等于0的时候返回null

```java
// 105. 从前序与中序遍历序列构造二叉树
public TreeNode buildTree(int[] preorder, int[] inorder) {
    // 把节点在中序序列中的位置放到HashMap中
    Map<Integer,Integer> map = new HashMap<>();
    for (int i = 0; i < inorder.length; i++) {
        map.put(inorder[i],i);
    }
    // 这里有点冗余了，可以放在递归方法里面实现
    TreeNode root = new TreeNode(preorder[0]);
    Integer inIndex = map.get(preorder[0]);
    int leftSize = inIndex-0;
    root.left = buildTreeHelper(preorder,inorder,map,1,0,leftSize);
    root.right = buildTreeHelper(preorder,inorder,map,0+leftSize+1,inIndex+1, inorder.length-inIndex-1);
    return root;
}
public TreeNode buildTreeHelper(int[] preorder,int[] inorder,Map<Integer,Integer> map,int preStart,int inStart,int size){
    // 递归结束条件
    if(size<=0){
        return null;
    }
    // 从前序遍历中找到根节点
    TreeNode node = new TreeNode(preorder[preStart]);
    // 确定根节点在中序序列中的位置，以及子树的size
    int inIndex = map.get(preorder[preStart]);
    int leftSize = inIndex - inStart;
    // 生成左右子树
    node.left = buildTreeHelper(preorder,inorder,map,preStart+1,inStart,inIndex - inStart);
    node.right = buildTreeHelper(preorder,inorder,map,preStart+leftSize+1,inIndex+1,inStart+size-inIndex-1);
    return node;
}
```



## 114.二叉树展开为链表

中序遍历，先对根节点的左子树展开，保存右子树的引用，将root.right = root.left，同时root.left置空，然后从根节点出发，找到已形成链表的尾节点，尾节点的right指向根节点的右子树（已经提前保存了右子树的引用）形成的链表

```java
// 114. 二叉树展开为链表
public void flatten(TreeNode root) {
    if(root == null){
        return;
    }
    // 先处理左子树
    flatten(root.left);
    // 保存右子树的引用
    TreeNode right = root.right;
    // 重连左右子树
    root.right = root.left;
    root.left = null;
    // 找到链表的尾节点
    TreeNode p = root;
    while(p.right != null){
        p=p.right;
    }
    // 尾节点连接右子树
    p.right = right;
    // 对右子树展开为链表
    flatten(right);
}
```



## 121.买卖股票的最佳时机

```java
// 121. 买卖股票的最佳时机
public int maxProfit(int[] prices) {
    int min = Integer.MAX_VALUE;
    int max = 0;
    for (int i = 0; i < prices.length; i++) {
        if(prices[i] < min){
            min = prices[i];
        }else if(prices[i] - min > max){
            max = prices[i] - min;
        }
    }
    return max;
}
```









## 124. 二叉树中的最大路径和

dfs，对于每一个节点，都计算以该节点为根节点的时候，路径的长度。计算方式就是分别计算两个子节点返回的路径长度，然后与当前节点的值相加，并于返回结果比较，取其中最大的值。

在返回值的时候，需要选择左子树或者右子树其中的一个分支形成一条没有分叉的路径，该路径的值最大

```java
int maxSum = Integer.MIN_VALUE;

public int maxPathSum(TreeNode root) {
    maxGain(root);
    return maxSum;
}

public int maxGain(TreeNode node) {
    if (node == null) {
        return 0;
    }
    // 递归计算左右子节点的最大贡献值
    // 只有在最大贡献值大于 0 时，才会选取对应子节点
    int leftGain = Math.max(maxGain(node.left), 0);
    int rightGain = Math.max(maxGain(node.right), 0);

    // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
    int priceNewpath = node.val + leftGain + rightGain;

    // 更新答案
    maxSum = Math.max(maxSum, priceNewpath);

    // 返回节点的最大贡献值
    return node.val + Math.max(leftGain, rightGain);
}

```





## 128.最长连续序列

最简单的做法就是先排序之后再遍历数组。但是题目要求时间复杂度为O(n)，所以不能进行排序。

考虑到使用空间换时间，将所有的数据先存放到哈希表中。然后对集合中的数x，判断判断x+1,x+2,x+3...是不是在集合中

```java
// 128. 最长连续序列
public int longestConsecutive(int[] nums) {
    // 把所有的数据保存到set集合中，同时可以去重
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    int res = 0;
    // 对于一个数x，判断x+1,x+2,x+3...是不是在集合中，同时维护最大长度
    // 如果对每一个x都判断的话，时间复杂度在比较坏的情况下会比较高，所以需要一定的优化。
    // 对于数x,如果x-1在集合中，就不要处理，因为在遍历到x-1的时候会处理x
    for (Integer num : set) {
        if(!set.contains(num-1)){
            int curLen = 1;
            int curNum = num;
            while(set.contains(curNum+1)){
                curNum++;
                curLen++;
            }
            res = Math.max(res,curLen);
        }
    }
    return res;
}
```



## 136. 只出现一次的数字

最初想法是使用哈希表完成，但是要求常数的额外空间，线性的时间复杂度，所以不能使用哈希表。

考虑使用位运算完成，通过不断异或（相同为0，不同为1），因为只有一个不重复的元素，其他重复的元素经过异或之后会变成0，最后结果就是不重复的那个元素

```java
public int singleNumber(int[] nums) {
    int res = nums[0];
    for(int i=1;i<nums.length;i++){
        res^=nums[i];
    }
    return res;
}
```



## 139.单词拆分

动态规划。

dp[i]=true表示字符串从0到i位置的字符可以被拆分。对于给定字符串的一个位置i，s[0,i]能被拆分的条件是：存在j, 0<=j<i，dp[j] = true，同时s[j+1,i]在给出的wordDict中

```java
// 139. 单词拆分
public boolean wordBreak(String s, List<String> wordDict) {
    Set<String> dict = new HashSet<>(wordDict);
    boolean[] dp = new boolean[s.length() + 1];
    dp[0] = true;
    for (int i = 1; i <= s.length(); i++) {
        for (int j = 0; j < i; j++) {
            if (dp[j] && dict.contains(s.substring(j, i))) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[s.length()];
}
```



## 141. 环形链表

使用哈希表存放所有遍历过的节点。在遍历过程中如果遇到了已经在表中的节点，就以为着出现了环

```java
// 141. 环形链表
public boolean hasCycle(ListNode head) {
    Set<ListNode> map = new HashSet<>();
    //ListNode newHead = new ListNode(-1,head);
    ListNode p = head;
    while(p != null){
        if (!map.add(p)) {
            return true;
        }
        p=p.next;
    }
    return false;
}
```

也可以使用快慢指针来解题。

快指针每一次走两步，慢指针每一次走一步。如果有环，快指针一定会在某一时刻追上慢指针

```java
public boolean hasCycle2(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast != null && fast.next != null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow == fast){
            return true;
        }
    }
    return false;
}
```



## 142.环形链表 II

```java
// 142. 环形链表 II
// 同样使用哈希表实现
public ListNode detectCycle(ListNode head) {
    ListNode p = head;
    Set<ListNode> set = new HashSet<>();
    while(p != null){
        if(set.contains(p)){
            return p;
        }else{
            set.add(p);
            p=p.next;
        }
    }
    return null;
}

// 解法2：同样是双指针，同解法一相同，快慢指针最终会相遇，
// 找到相交节点位置，然后再有两个指针p，q一个从头节点开始，一个从相交节点开始，每次移动一步，二者相交的位置就是环的入口
public ListNode detectCycle2(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast != null && fast.next != null){
        slow = slow.next;
        fast = fast.next.next;
        if(slow == fast){
            break;
        }
    }
    if(fast == null || fast.next == null){
        return null;
    }
    // 此时slow指向的是相交的节点
    ListNode p = head, q = slow;
    while(p != q){
        p = p.next;
        q = q.next;
    }
    return p;
}
```



## TODO：146. LRU 缓存



## TODO：148.排序链表



## 152.乘积最大子数组

用一个maxArray表示以当前数字结尾的子数组的最大乘积

用一个minArray表示以当前数字结尾的子数组的最小乘积（保存最小乘积是因为可能出现负负得正的情况）

在取最大值的时候可能有三种情况（最小值同理）：
当前数字与前面的子数组最大值相乘，当前数字与前面子数组最小值相乘，仅为当前数字

```java
// 152. 乘积最大子数组
public int maxProduct(int[] nums) {
    int[] maxArray = new int[nums.length];
    int[] minArray = new int[nums.length];
    Arrays.fill(maxArray,1);
    Arrays.fill(minArray,1);
    maxArray[0] = nums[0];
    minArray[0] = nums[0];
    for (int i = 1; i < nums.length; i++) {
        // 当前位置的最大值可能从下面三个数中取得，分别是和之前的数组最大值相乘，和之前的数组最小值相乘，或者是这个数字本身
        maxArray[i] = Math.max(nums[i] * maxArray[i-1],Math.max(nums[i] * minArray[i-1],nums[i]));
        // 保存最小值是因为可能出现负负得正的情况
        minArray[i] = Math.min(nums[i] * maxArray[i-1],Math.min(nums[i] * minArray[i-1],nums[i]));
    }
    int res = Integer.MIN_VALUE;
    for (int i = 0; i < maxArray.length; i++) {
        res = Math.max(res,maxArray[i]);
    }
    return res;
}
```



## 155.最小栈

使用两个栈，一个是数据栈，存放push的数据，一个是最小值栈，存放当前栈中的最小值

在push的时候，数据直接push到数据栈中，还需要比较当前push的元素与最小值栈的栈顶元素的大小，大于就push最小值栈顶元素，小于就说明这个元素是最小的，push当前元素

```java
class MinStack {
    private Deque<Integer> norStack;
    private Deque<Integer> minStack;


    public MinStack() {
        norStack = new LinkedList<>();
        minStack = new LinkedList<>();
    }

    public void push(int val) {
        // 入栈操作，将val与辅助栈的栈顶元素进行比较，
        //  如果小于栈顶元素，说明val入栈之后，栈中的最小值为val，将辅助栈的栈顶元素设置为val
        //  如果大于栈顶元素，说明val入栈之后，栈中的最小值仍未栈顶元素，将栈顶元素再次push进辅助栈
        norStack.push(val);
        if (minStack.size() == 0) {
            minStack.push(val);
        } else {
            int temp = minStack.peek();
            minStack.push(temp > val ? val : temp);
        }
    }

    public void pop() {
        norStack.pop();
        minStack.pop();
    }

    public int top() {
        return norStack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }

    public static void main(String[] args) {
        MinStack minStack1 = new MinStack();
        minStack1.push(-2);
        minStack1.push(1);
        minStack1.push(-3);
        System.out.println(minStack1.getMin());
        minStack1.pop();
        System.out.println(minStack1.getMin());
        System.out.println(minStack1.top());
    }
}
```





## 160.相交链表

```java
// 160. 相交链表
// 方法1，使用set存储每一个遍历过的节点
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    Set<ListNode> set = new HashSet<>();
    ListNode p = headA;
    while(p != null){
        set.add(p);
        p=p.next;
    }
    p = headB;
    while(p != null){
        if(set.contains(p)){
            return p;
        }else{
            set.add(p);
        }
        p=p.next;
    }
    return null;
}

// 方法2，双指针，我走过你走过的路，那我们一定会相拥
public ListNode getIntersectionNode2(ListNode headA, ListNode headB) {
    if(headA == null || headB == null){
        return null;
    }
    ListNode a = headA, b = headB;
    while(a != b){
        a = a == null ? headB : a.next;
        b = b == null ? headA : b.next;
    }
    return a;
}
```



## TODO:169. 多数元素



## 198.打家劫舍

```java
// 198. 打家劫舍
// 动态规划，dp[i]表示到第i间房子所能获得的最大收益
// 针对每一间房子，都可以选择偷或者不偷。如果偷，收益就是dp[i-2] + nums[i]
// 如果不偷，收益就是dp[i-1]， 所以dp[i] = max(dp[i-2] + nums[i],dp[i-1]);
public int rob(int[] nums) {
    if(nums.length == 1){
        return nums[0];
    }
    int[] dp = new int[nums.length];
    dp[0] = nums[0];
    dp[1] = Math.max(dp[0],nums[1]);
    for(int i=2;i<nums.length;i++){
        dp[i] = Math.max(dp[i-2] + nums[i],dp[i-1]);
    }
    return dp[nums.length-1];

}
```





## 200.岛屿数量

dfs直接秒了！！

在外层先找到每一个岛屿的入口（岛屿上的一个节点），然后从这个入口开始向四周进行深度优先搜索，将一个岛屿的所有节点的访问标志置为true，也就是visited

```java
// 200. 岛屿数量
public int numIslands(char[][] grid) {
    boolean[][] visited = new boolean[grid.length][grid[0].length];
    int res = 0;
    for (int i = 0; i < grid.length; i++) {
        for (int j = 0; j < grid[i].length; j++) {
            if(grid[i][j] == '1' && !visited[i][j]){
                visited[i][j]=  true;
                numIslandsHelper(grid,visited,i,j);
                res++;
            }
        }
    }
    return res;
}
public int[][] moveFor200 = new int[][]{{-1,0},{0,1},{1,0},{0,-1}};

public void numIslandsHelper(char[][] grid,boolean[][] visited,int indexI,int indexJ){
    for (int i = 0; i < moveFor200.length; i++) {
        int newI = indexI + moveFor200[i][0], newJ = indexJ + moveFor200[i][1];
        if(isPosValidFor200(newI,newJ,grid.length,grid[0].length) && grid[newI][newJ] == '1' && !visited[newI][newJ]){
            visited[newI][newJ] = true;
            numIslandsHelper(grid,visited,newI,newJ);
        }
    }
}

public boolean isPosValidFor200(int i,int j,int maxI,int maxJ){
    return i>=0 && i < maxI && j >= 0 && j < maxJ;
}
```



## 206.反转链表

使用了三个指针，分别是p指向待处理节点的前一个结点，q指向当前待处理的节点，就是需要修改next字段的节点，j指向当前节点的下一个节点，因为需要修改next，所以需要提前保存next节点

```java
public ListNode reverseList(ListNode head) {
    if(head == null || head.next == null){
        return head;
    }
    ListNode p = head, q = head.next;
    head.next = null;
    while(q != null){
        ListNode j = q.next;
        q.next = p;
        p=q;
        q=j;
    }
    return p;
}
```



递归法

先写好递归出口，也就是找到了新的头节点

反转head后面的链表，也就是调用reverseList(head.next)，得到了新的头节点

这样head之后的链表已经反转完成，再将head.next.next = head 这样就完成了head的反转，然后将

head.next = null 将head变为尾节点就完成了整个链表的反转

```java
// 206.反转链表-2
public ListNode reverseList2(ListNode head) {
    if(head == null || head.next == null){
        return head;
    }
    // 新的头节点
    ListNode newHead = reverseList2(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}
```



## 207.课程表

以前驱课程（要先学习的课程）为前驱节点，后继课程（后学习的课程）为后继节点，建立邻接表形式的有向图。维护每一个节点的入度。如果入度为0，就表示没有前置课程需要学习。

利用广度优先遍历，学习入度为0的课程，同时将该课程指向的节点的入度减一，意味着这个课程已经学习结束，同时将后继课程入度为0的节点加入队列中

```java
// 207. 课程表
// 所有节点的入度
private int[] inDegree;
// 邻接表的形式构成有向图
private List<List<Integer>> edges;
public boolean canFinish(int numCourses, int[][] prerequisites) {
    // 初始化
    inDegree = new int[numCourses];
    edges = new ArrayList<>();
    for (int i = 0; i < numCourses; i++) {
        edges.add(new ArrayList<>());
    }
    // 建立邻接表
    for (int[] prerequisite : prerequisites) {
        // 先学习的课程指向后学习的课程
        edges.get(prerequisite[1]).add(prerequisite[0]);
        // 入度++
        inDegree[prerequisite[0]]++;
    }

    Deque<Integer> deque = new LinkedList<>();
    for (int i = 0; i < numCourses; i++) {
        if(inDegree[i] == 0){
            deque.addLast(i);
        }
    }
    int count = 0;
    while(!deque.isEmpty()){
        Integer index = deque.pollFirst();
        count++;
        for (Integer i : edges.get(index)) {
            inDegree[i]--;
            if(inDegree[i] == 0){
                deque.addLast(i);
            }
        }
    }
    return count == numCourses;
}
```



## 208.实现 Trie (前缀树)

我认为这个前缀树就是一种多叉树的形式。

```java
class Trie {
    //子节点
    private Trie[] children;
    //该节点是否是一个字符串的结尾
    private boolean isEnd;

    public Trie() {
        children = new Trie[26];
        isEnd = false;
    }

    public void insert(String word) {
        Trie node = this;
        for(int i=0;i<word.length();i++){
            int index = word.charAt(i)-'a';
            //对应位置还没有子节点，就创建子节点
            if(node.children[index] == null){
                node.children[index] = new Trie();
            }
            node = node.children[index];
        }
        //结束循环，此时node已经指向最后一个节点了
        node.isEnd = true;
    }

    public boolean search(String word) {
        Trie node = searchPrefix(word);
        //必须要保证最后一个结点是字符串的结尾
        return node != null && node.isEnd;
    }

    public boolean startsWith(String prefix) {
        return  searchPrefix(prefix) != null;
    }
    
    public Trie searchPrefix(String word){
        Trie node = this;
        for(int i=0;i<word.length();i++){
            int index = word.charAt(i) - 'a';
            if(node.children[index] == null){
                return null;
            }
            node = node.children[index];
        }
        return node;
    }
}
```



## 215. 数组中的第K个最大元素

最简单的做法就是先排序，然后找到第k个最大元素。

高级一点的做法就是堆排序，维护一个大根堆，也就是最大的元素在堆顶。然后移除k次堆顶元素，这样就得到了第k个最大元素。

不过这两种方法时间复杂度都是O(n * logn)，第一种快排复杂度为O(n * logn)，取出为O(1)

第二种建堆为O(n)，删除的总成本为O(n * logn)

题目要求的时间复杂度为O(n)，所以这两种方法不能满足要求，可以使用基于快速排序的选择方法。

### 堆排序：

堆排序的关键在于如何建堆，如何调整堆以及如何从堆顶删除一个元素

建堆：给定一个数组（按照完全二叉树的形式给出），对这个数组建堆的过程就是从最后一个非叶子节点出发，到第一个节点结束，对中间遇到的每一个非叶子节点进行调整堆的动作。`nums.length/2-1`就是最后一个非叶子节点的下标

调整堆：得到了一个节点，需要保证以这个节点为跟的树是大根堆或者小根堆。这里使用的是大根堆。得到左子结点和右子节点`（left = index * 2 + 1, right = index * 2 + 2）`，获得二者中值较大的一个，然后与跟节点交换值。然后对被交换的子节点进行调整堆的动作。

删除堆顶节点：将堆顶元素与最后一个元素进行交换，然后从堆顶进行调整堆的动作，这里要注意将堆的大小减1，因为已经删除了一个元素。

```java
// 215. 数组中的第K个最大元素、
public int findKthLargest(int[] nums, int k) {
    buildMaxHeapFor215(nums);
    for (int i = 0; i < k; i++) {
        deleteMaxHeapFor215(nums,nums.length - i);
    }
    // Arrays.toString(nums);
    return nums[nums.length - k];
}

public void buildMaxHeapFor215(int[] nums){
    for (int i = nums.length/2 -1; i >= 0; i--) {
        adjustMaxHeapFor215(nums,nums.length,i);
    }
}

public void adjustMaxHeapFor215(int[] nums,int size,int index){
    // 找到左右子节点
    int left = index * 2 + 1, right = index * 2 + 2;
    int max = index;
    if(left < size && nums[left] > nums[max]){
        max = left;
    }
    if(right < size && nums[right] > nums[max]){
        max = right;
    }
    if(max != index){
        // 发生调整
        swap(nums,index,max);
        adjustMaxHeapFor215(nums,size,max);
    }
}

public void deleteMaxHeapFor215(int[] nums,int size){
    // 将堆顶的元素与最后一个元素交换
    swap(nums,0,size-1);
    // 然后从堆顶元素开始调整堆
    adjustMaxHeapFor215(nums,size-1,0);
}

public void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```



### 基于快速排序的选择方法：

```java


```



## 221. 最大正方形

在外层的遍历矩阵中的所有元素，找到每一个为1的位置，也就是正方形的左上角，然后从这个左上角进行搜索。每一次搜索的长度都加1，搜索过程中主要是检查右边是否为1，下边是否为1，以及右下角是否为1，如果都满足，就计算最大正方形，`res = max(res, (len + 1) ^ 2)`，然后继续搜索，len+1。

```java
//221. 最大正方形
    private int ans221 = 0;
    public int maximalSquare(char[][] matrix) {
        for(int i=0;i<matrix.length;i++){
            for (int j = 0; j < matrix[i].length; j++) {
                if(matrix[i][j] == '1'){
                    ans221 = Math.max(ans221,1);
                    bfsFor221(matrix,i,j,1);
                }
            }
        }
        return ans221;
    }
    //i，j表示左上角点，len表示边长
    private void bfsFor221(char[][] matrix,int i,int j,int len){
        if(checkFor221(matrix.length,matrix[i].length,i+len,j+len)){
            //检查i+len行
            for(int k = j;k<j+len;k++){
                if(matrix[i+len][k] != '1'){
                    return;
                }
            }
            //检查j+len列
            for(int k = i;k<i+len;k++){
                if(matrix[k][j+len] != '1'){
                    return;
                }
            }
            //检查
            if(matrix[i+len][j+len] != '1'){
                return;
            }
            ans221 = Math.max(ans221,(len+1)*(len+1));
            bfsFor221(matrix,i,j,len+1);
        }
    }
    private boolean checkFor221(int size1,int size2,int i,int j){
        return i<size1 && j < size2;
    }
```



## 226.翻转二叉树

后续遍历，先处理左右子树，然后更改root的左右指针的指向，返回root

```java
// 226. 翻转二叉树
public TreeNode invertTree(TreeNode root) {
    if(root == null){
        return null;
    }
    TreeNode tempRight = root.right;
    root.right = invertTree(root.left);
    root.left = invertTree(tempRight);
    return root;
}
```



## 234.回文链表

先使用快慢指针，找到链表的中点。如果有奇数个元素，那么slow会指向中间元素，如果为偶数个元素，那么slow会指向后一半的第一个元素。

反转后一半链表，此时然后同时遍历两段链表，判断是不是回文。需要注意循环结束的条件

```java
// 234. 回文链表
public boolean isPalindrome(ListNode head) {
    if(head == null || head.next == null){
        return true;
    }
    ListNode slow = head;
    ListNode fast = head;
    while(fast != null && fast.next != null){
        slow = slow.next;
        fast = fast.next.next;
    }
    // 如果为奇数，slow正好指在中间，如果为偶数，slow指向后一半的第一个位置
    // 反转后面的链表
    ListNode reverseHead = reverseList(slow);
    ListNode p = head, q = reverseHead;
    while(p != slow && q != null && p.val == q.val){
        p=p.next;
        q=q.next;
    }
    ListNode temp = slow;
    // 将链表反转回去
    // slow = reverseList(reverseHead);
    return p == temp;
}
```



## 236.二叉树的最近公共祖先

对于两个给定的节点p，q，如果其中有一个点的值与当前节点root相同，那么当前节点root一定就是最近公共祖先

如果不是就要检查p是不是左子节点，q是不是左子节点

如果p，q一个是左子节点，一个是右子节点，那么返回root

如果p，q都是左，那么就对root.left重复上述操作

如果p，q都是右，那么就对root.right重复上述操作

```java
// 236. 二叉树的最近公共祖先
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if(root == null){
        return root;
    }
    // 如果有一个节点是当前节点，那么就没必要向下搜索了
    if(root.val == p.val || root.val == q.val){
        return root;
    }
    // 判断p是不是左子节点，q是不是右子节点
    boolean pIsLeftSubNode = isSubNode(root.left,p);
    boolean qIsLeftSubNode = isSubNode(root.left,q);
    // 如果p是左子节点，同时q是右子节点  或者 p是右子节点同时q是左子结点  返回root
    if(pIsLeftSubNode && !qIsLeftSubNode || (!pIsLeftSubNode && qIsLeftSubNode)){
        return root;
    }
    // 如果p,q都是左子节点
    if(pIsLeftSubNode && qIsLeftSubNode){
        return lowestCommonAncestor(root.left,p,q);
    }
    // 如果p,q都是右子节点
    if(!pIsLeftSubNode && !qIsLeftSubNode){
        return lowestCommonAncestor(root.right,p,q);
    }
    return null;
}
public boolean isSubNode(TreeNode root,TreeNode node){
    if(root == null){
        return false;
    }
    if(root == node){
        return true;
    }
    return isSubNode(root.left,node) || isSubNode(root.right,node);
}
```



## 238.除自身以外数组的乘积

要求不能使用除法，就不能计算所有元素乘积然后依次除掉某一个位置的元素

要求需要在线性的时间复杂度内完成，就不能双重循环求乘积

可以使用两个乘积数组完成，一个数组L，表示除当前位置外，该位置左侧所有元素的乘积

数组R表示除当前位置外，该位置右侧所有元素的乘积。

计算结果的时候只需要让L和R对应位置相乘即可。`res[i] = L[i] * R[i]`

需要注意L的填充顺序为从左到右，L[0] = 1，R的填充顺序为从右到左，R[R.length-1] = 1

```java
// 238. 除自身以外数组的乘积
public int[] productExceptSelf(int[] nums) {
    if (nums == null || nums.length == 0) {
        return nums;
    }
    int[] L = new int[nums.length];
    int[] R = new int[nums.length];
    L[0] = 1;
    R[R.length - 1] = 1;
    for (int i = 1; i < L.length; i++) {
        L[i] = nums[i - 1] * L[i - 1];
    }
    for (int i = R.length - 2; i >= 0; i--) {
        R[i] = nums[i + 1] * R[i + 1];
    }
    int[] res = new int[nums.length];
    for (int i = 0; i < nums.length; i++) {
        res[i] = L[i] * R[i];
    }
    return res;
}
```

题目进一步要求使用O(1)的空间复杂度，由于输出数组不在空间复杂度范围内，所以L和R可以用输出数组来表示，另外有两个变量，来表示除自身外左侧所有位置的乘积以及除自身外右侧所有位置的乘积

输出数组先初始化为1，然后使用左侧乘积填充输出数组，之后使用右侧乘积与输出数组相乘完成



## 240.搜索二维矩阵 Ⅱ

解法一：由于每一行都是有序的，所以可以对每一行进行二分查找

解法二：利用了给定矩阵的性质，每一行、每一列都是递增的。假设当前位置在（x，y），我们可以搜索以该位置为右上角，matrix的左下角为左下角的矩阵，每一次只需要比较矩阵的右上角与target的大小，如果等于直接返回；如果大于，就说明该列之后的元素都大于taget，y--；如果小于，x++；

```java
// 240. 搜索二维矩阵 II
public boolean searchMatrix(int[][] matrix, int target) {
    for (int[] arr : matrix) {
        if(arr[0] > target){
            return false;
        }
        int index = binSearchFor240(arr,0,arr.length - 1, target);
        System.out.println(index);
        if(index >= 0){
            return true;
        }
    }
    return false;
}
public int binSearchFor240(int[] arr, int start, int end, int target){
    int left = start, right = end;
    while(left <= right){
        int mid = (left + right) >> 1;
        if(arr[mid] == target){
            return mid;
        }else if(arr[mid] > target){
            right = mid - 1;
        }else{
            left = mid + 1;
        }
    }
    // 没找到就返回-1
    return -1;
}
// 240. 搜索二维矩阵 II-2
// Z字形查找，利用给定矩阵的性质
public boolean searchMatrix2(int[][] matrix, int target) {
    int m = matrix.length, n = matrix[0].length;
    int x = 0, y = n-1;
    while(x < m && y >= 0){
        if(matrix[x][y] == target){
            return true;
        }else if(matrix[x][y] > target){
            y--;
        }else{
            x++;
        }
    }
    return false;
}
```



## TODO：279.完全平方数

应该是背包问题





## 283.移动零

```java
// 283. 移动零
public void moveZeroes(int[] nums) {
    int left = 0, right = 0;
    while(left < nums.length){
        while(left < nums.length && nums[left] != 0){
            left++;
        }
        if(left == nums.length){
            return;
        }
        // left指向第一个为0的位置
        right = left + 1;
        while(right < nums.length && nums[right] == 0){
            right++;
        }
        if(right == nums.length){
            return;
        }
        nums[left] = nums[right];
        nums[right] = 0;
    }
    
}

// 官方题解的写法更好
// left指向处理好的序列的末尾，right指向待处理的序列的开头
// 如果遇到连续的不为0的位置，left和right会同时前进，直到遇到0，left停止，right继续找不为0的位置
public void moveZeroes(int[] nums) {
	int left = 0, right = 0;
    while(right < nums.length){
		if(nums[right] != 0){
			swap(nums,left,right);
             left++;
        }
        right++;
    }
}
```



## TODO：287. 寻找重复数

使用位运算解决。还有一种方法是使用快慢指针解决（Floyd判圈 和之前的环形链表比较像）



## 300. 最长递增子序列

动态规划，对于每一个位置，记录以该位置结尾的最长子序列长度。

遍历每一个位置，对于当前位置i，需要找到0 <= j <= i-1 之间，满足nums[i] > nums[j]的位置j，维护一个最大的长度，也就是preMax = max(preMax, dp[j])

```java
// 300. 最长递增子序列
public int lengthOfLIS(int[] nums) {
    // dp[i]表示以nums[i]结尾的最长子序列长度
    int[] dp = new int[nums.length];
    // 初始化dp[0]
    dp[0] = 1;
    // dp[i] = max{dp[j]} + 1,0<=j<i且nums[j] < nums[i]
    int res = 0;
    for (int i = 1; i < nums.length; i++) {
        // dp[i] = 1;
        int preMax = 0;
        for (int j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                // 保证递增
                preMax = Math.max(preMax, dp[j]);
            }
        }
        dp[i] = preMax + 1;
        res = Math.max(dp[i], res);
    }
    return res;
}
```



## TODO：309.最佳买卖股票时机含冷冻期



## TODO：322.零钱兑换



## TODO：337.打家劫舍Ⅲ



## 338.比特位计数

方法1：对每一个数字都去求二进制中1的个数，使用位运算的方式

方法2：利用 x = x & (x - 1) 可以将x中最后一个1置为0的性质，快速求1的个数

方法3：DP，对于每一个2的整数幂，都只有一个1，将这个整数置为highBit，其他数只需要在这个基础上求就可以了。res[i] = res[i - highBit] + 1

```java
// 338. 比特位计数
// 最简单最直接的思路就是遍历每一个数，然后计算其二进制中1的个数
public int[] countBits(int n) {
    int[] res = new int[n + 1];
    res[0] = 0;
    for(int i=1;i<=n;i++){
        int bit = 1;
        while(bit <= i){
            if((bit & i) != 0){
                res[i]++;
            }
            bit = bit << 1;
        }
        //res[i] = oneCounts(i);
    }
    return res;
}
// 有一种升级的方法可以快速求一个数的二进制中1的位数 x = x & (x-1)可以将x的最后一个1置为0
public int oneCounts(int n) {
    int res = 0;
    while(n > 0){
        n&=(n-1);
        res++;
    }
    return res;
}
// 别的方法就是nb的动态规划
public int[] countBits2(int n) {
    int[] res = new int[n + 1];
    int highBit = 0;
    for (int i = 0; i <= n; i++) {
        if((i & (i - 1)) == 0){
            highBit = i;
        }
        res[i] = res[i - highBit] + 1;
    }
    return res;
}
```



## TODO：347.前K个高频元素

用堆就行了



## 394.字符串解码

使用栈，遇到数字就入栈，遇到左括号和字母也入栈，遇到右括号就出栈，直到栈顶为左括号。将出栈的字符逆序得到一个字符串，然后出栈得到重复次数，将字符串重复一定次数之后入栈。进行下一次循环



方法中有一些重复的代码没有抽取出来

```java
// 394. 字符串解码
public String decodeString(String s) {
    int index = 0;
    Deque<String> deque = new LinkedList<>();
    //int count = 0;
    while(index < s.length()){
        char ch = s.charAt(index);
        if(ch >= '0' && ch <= '9'){
            // 是数字
            int count = 0;
            char ch1 = s.charAt(index);
            while(ch1 >= '0' && ch1 <= '9'){
                count = count * 10 + ch1 - '0';
                index++;
                ch1 = s.charAt(index);
            }
            // 数字计算完需要入栈
            deque.push(String.valueOf(count));
        }else if(ch == '[' || (ch >= 'a' && ch <= 'z')){
            // 是左括号或者字母  入栈
            deque.push(String.valueOf(ch));
            index++;
        }else{
            // 是右括号
            // 先出栈，直到遇到左括号
            LinkedList<String> temp = new LinkedList<>();
            while(!"[".equals(deque.peek())){
                temp.addFirst(deque.pop());
            }
            deque.pop(); // 左括号出栈
            // 拼接字符串
            StringBuilder builder = new StringBuilder();
            for (String s1 : temp) {
                builder.append(s1);
            }
            String tempStr = builder.toString();
            // 得到重复次数
            int count = Integer.parseInt(deque.pop());
            // 由于之前的builder里面已经有有一份了，所以这里需要减少一次复制
            for (int i = 0; i < count - 1; i++) {
                builder.append(tempStr);
            }
            // 将拼接好的字符串入栈
            deque.push(builder.toString());
            index++;
        }
        //index++;
    }
    StringBuilder resBuilder = new StringBuilder();
    LinkedList<String> resList = new LinkedList<>();
    while(!deque.isEmpty()){
        resList.addFirst(deque.pop());
    }
    for (String s1 : resList) {
        resBuilder.append(s1);
    }
    return resBuilder.toString();
}
```



## TODO：399. 除法求值

可以建立一个有向图，节点到节点之间的路径权值就是前驱节点到后继节点的比值

有点难其实，现在不太想做！！





## 406. 根据身高重建队列

直接贪心算法开整。

先对原数组进行排序，先按照身高升序排序，对于身高相同的人，需要按照前面相同身高的人数的降序进行排序。

遍历原数据，对于每一个人，得到其前面应该有热人数，然后在结果数组中跳过若干次还没有放置元素的位置。（跳过的次数等于前面的人数+1）

这里的贪心就在于先排最小的，最小的肯定是可以确定位置的。对于相同身高的先排前面人数多的，这样前面人数少的就会排在多的前面

```java
// 406. 根据身高重建队列
public int[][] reconstructQueue(int[][] people) {
    // 对数组进行排序，
    Arrays.sort(people, new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            if(o1[0] == o2[0]){
                return o2[1] - o1[1];
            }
            return o1[0] - o2[0];
        }
    });
    /* for (int[] person : people) {
        for (int i : person) {
            System.out.print(i + " ");
        }
        System.out.println();
    } */
    int len = people.length;
    int[][] res = new int[len][2];
    for (int[] temp : res) {
        temp[0] = -1;
    }
    for (int i = 0; i < len; i++) {
        int h = people[i][0], k = people[i][1] + 1;
        for (int j = 0; j < len; j++) {
            if(res[j][0] == -1){
                k--;
                if(k == 0){
                    res[j][0] = h;
                    res[j][1] = people[i][1];
                }
            }
        }
    }
    return res;
}
```



## 416. 分割等和子集

```java
// 416. 分割等和子集
/*
思路：01背包问题
先求出数组中所有元素的和，然后除2，可以整除就意味着可以分成两个背包
可以得到每个背包的size
01背包先遍历物品，再遍历背包，而且背包需要倒序遍历
 */
public boolean canPartition(int[] nums) {
    if(nums == null || nums.length == 0){
        return false;
    }
    int sum = 0;
    for (int num : nums) {
        sum += num;
    }
    if (sum % 2 != 0){
        return false;
    }
    int bagSize = sum/2;
    // 得到了背包的大小，然后就是看是否可以装满这个背包
    Arrays.sort(nums);
    int[] dp = new int[bagSize + 1];
    // 外层遍历物品
    for (int i = 0; i < nums.length; i++) {
        // 内层遍历背包 倒序遍历
        for (int j = dp.length - 1; j >= 0; j--) {
            if(j-nums[i] >= 0){
                dp[j] = Math.max(dp[j],dp[j-nums[i]] + nums[i]);
            }

        }
    }
    return dp[dp.length-1] == bagSize;
}
```



## 437.路径总和 III

```java
// 437. 路径总和 III
public int pathSum(TreeNode root, long targetSum) {
    if(root == null){
        return 0;
    }
    int res = rootSum(root,targetSum);
    res+=pathSum(root.left,targetSum);
    res+=pathSum(root.right,targetSum);
    return res;
}

private int rootSum(TreeNode root, long targetSum) {
    int res = 0;
    if(root == null){
        return res;
    }
    int val = root.val;
    if(val == targetSum){
        res++;
    }
    res+=rootSum(root.left,targetSum - val);
    res+=rootSum(root.right,targetSum - val);
    return res;
}
```



## 438. 找到字符串中所有字母异位词

滑动窗口做的

```java
// 438. 找到字符串中所有字母异位词
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> list = new ArrayList<>();
    if(s == null || s.length() == 0 || p == null || p.length() == 0){
        return list;
    }
    // 使用数组保存p中出现单词的个数
    int[] charNum = new int[26];
    for (int i = 0; i < p.length(); i++) {
        charNum[p.charAt(i) - 'a']++;
    }

    // 窗口的左右边界
    int left = 0, right = p.length() - 1;

    while(right < s.length()){
        if(charNum[s.charAt(left) - 'a'] != 0){
            // 遇到了在p中的字符
            // 在窗口中查看是否是异位词
            int[] tempCharNum = Arrays.copyOf(charNum,26);
            int hitCount = 0;
            for (int i = left; i <= right; i++) {
                int index = s.charAt(i) - 'a';
                if(tempCharNum[index] != 0){
                    hitCount++;
                    tempCharNum[index]--;
                }else{
                    break;
                }
            }
            if(hitCount == p.length()){
                list.add(left);
            }
        }else{

        }
        // 窗口右移
        left++;
        right++;
    }
    return list;
}
```



## 448. 找到所有数组中消失的数字

```java
// 448. 找到所有数组中消失的数字
// 方法1，使用哈希表解决，时空复杂度均为O(n)
public List<Integer> findDisappearedNumbers(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    List<Integer> res = new ArrayList<>();
    for(int i=1;i<=nums.length;i++){
        if(!set.contains(i)){
            res.add(i);
        }
    }
    return res;
}
// 方法2，原地修改，避免了哈希表浪费的空间
// 没遇到一个数x，就将nums[x-1]加上n，这样遇到了数的位置的值一定是大于n，没遇到的数的位置就是<n的
public List<Integer> findDisappearedNumbers2(int[] nums) {
    for (int num : nums) {
        int x = (num - 1) % nums.length;
        nums[x]+=nums.length;
    }
    List<Integer> res = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        if(nums[i] <= nums.length){
            res.add(i+1);
        }
    }
    return res;
}
```



## 538. 把二叉搜索树转换为累加树

中序遍历，先处理右子节点，然后处理中间节点，将累加的和记录下来，最后处理左子结点

```java
// 538. 把二叉搜索树转换为累加树
private int sumFor538 = 0;

public TreeNode convertBST(TreeNode root) {
    dfsFor538(root);
    return root;
}

public void dfsFor538(TreeNode root) {
    if (root == null) {
        return;
    }
    // 先找右节点
    dfsFor538(root.right);
    root.val += sumFor538;
    // 更新总和
    sumFor538 = root.val;
    // 最后找左节点
    dfsFor538(root.left);
}
```



## 543. 二叉树的直径

后续遍历统计最长的路径中的节点个数，最后结果的边的数量就是节点个数-1

```java
// 543. 二叉树的直径
public int diameterOfBinaryTree(TreeNode root) {
    if(root == null){
        return 0;
    }
    int temp = diameterOfBinaryTreeHelper(root);
    return Math.max(temp,resFor543) - 1;
}
// 任意两个节点之间的节点个数
public int resFor543 = 1;
// 返回节点个数最多的路径上的节点个数
public int diameterOfBinaryTreeHelper(TreeNode root){
    if(root == null){
        return 0;
    }
    // 后续遍历
    int left = diameterOfBinaryTreeHelper(root.left);
    int right = diameterOfBinaryTreeHelper(root.right);
    resFor543 = Math.max(resFor543,left + right + 1);
    // 返回左右路径中最大的一个
    return Math.max(left, right) + 1;
}
```



## 560.和为k的子数组

```java
// 560. 和为 K 的子数组
// 方法1，枚举出所有的子数组,利用子数组的右边界来保证子数组的唯一性
public int subarraySum(int[] nums, int k) {
    int res = 0;
    // 枚举右边界
    for (int i = 0; i < nums.length; i++) {
        int sum = 0;
        // 枚举左边界
        for (int j = i; j >= 0; j--) {
            sum+=nums[j];
            if(sum == k){
                res++;
            }
        }
    }
    return res;
}

// 方法2，优化版本
public int subarraySum2(int[] nums, int k) {
    int pre = 0, res = 0;
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1);
    for (int num : nums) {
        pre += num;

        if (map.containsKey(pre - k)) {
            res += map.get(pre - k);
        }
        map.put(pre, map.getOrDefault(pre, 0) + 1);
    }
    return res;
}
```



## 581.最短无序连续子数组

方法1，对数组进行排序，然后找到有序前缀和有序后缀，中间剩余的部分就是要求的最短无序连续子数组

```java
// 581.最短无序连续子数组
// 方法1，将数组划分成numsA,numsB,numsC三部分，numsA是有序的前缀，numsB是无序的中间部分，numsC是有序的后缀
// 对原数组进行排序，然后一一对比找到numsA，numsC，这样中间剩余的部分就是要求的无序数组
public int findUnsortedSubarray(int[] nums) {
    // 先将数组进行排序，然后找到相同的前缀和相同的后缀，中间部分就是要求的
    int[] sortedNums = Arrays.copyOf(nums, nums.length);
    int left = 0, right = nums.length - 1;
    while(left <= right){
        if (sortedNums[left] != nums[left]){
            break;
        }
        left++;
    }
    while(left <= right){
        if(sortedNums[right] != nums[right]){
            break;
        }
        right--;
    }
    return right - left + 1;
}
```

方法2，一次遍历，不会做。。

## 617.合并二叉树

```java
// 617. 合并二叉树
public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
    return mergeTreesHelper(root1,root2);
}

public TreeNode mergeTreesHelper(TreeNode root1, TreeNode root2){
    if(root1 == null || root2 == null){
        return root1 == null ? root2 : root1;
    }
    root1.val+=root2.val;
    root1.left = mergeTreesHelper(root1.left,root2.left);
    root1.right = mergeTreesHelper(root1.right,root2.right);
    return root1;
}
```

## TODO：621.任务调度器

## 647.回文子串

中心拓展法，枚举所有可能的回文串的中心位置，然后从中心位置向两段拓展。

枚举中心位置的时候需要注意中心位置有两种可能，可以只有一个字符或者有两个字符

```java
// 647. 回文子串
// 对于回文串的题目，使用中心拓展法，枚举所有可能的回文串的中心
public int countSubstrings(String s) {
    char[] chars = s.toCharArray();
    int res = 0;
    for (int i = 0; i < chars.length; i++) {
        res+=countSubstringsHelper(chars,i,i);
        res+=countSubstringsHelper(chars,i,i+1);
    }
    return res;
}
// 返回回文子串的个数
public int countSubstringsHelper(char[] chars, int left, int right){
    int count = 0;
    while(left >= 0 && right < chars.length && chars[left] == chars[right]){
        count++;
        left--;
        right++;
    }
    return count;
}
```



## 739.每日温度

单调栈

得到每一天的温度，先将栈中已有的温度与当前温度比较，将小于今天温度的元素出栈，最后将今天的温度入栈

最后可能栈中仍有部分元素，这些元素对应的日期返回结果为0，意味着在后面的日期中没有比该天温度更大的了

```java
//739. 每日温度
public int[] dailyTemperatures(int[] temperatures) {
    int[] res = new int[temperatures.length];
    Deque<Integer> stack = new LinkedList<>();
    for(int i=0;i< temperatures.length;i++){
        int cur = temperatures[i];
        while(!stack.isEmpty() && cur > temperatures[stack.peek()]){
            int preIndex = stack.pop();
            res[preIndex] = i - preIndex;
        }
        stack.push(i);
    }
    return res;
}
```



# 剑指Offer

## 3. 数组中重复的数字

```java
//剑指 Offer 3.数组中重复的数字
// 解法1，利用set集合不重复的性质
public int findRepeatNumber(int[] nums) {
    Set set = new HashSet();
    for (int num : nums) {
        if (!set.add(num)) {
            return num;
        }
    }
    return -1;
}
```



## 4.二维数组中的查找

```java
//解法1 暴力查找
public boolean findNumberIn2DArray(int[][] matrix, int target) {
	if(matrix == null || matrix.length == 0){
        return false;
    }

    for (int i = 0; i < matrix.length; i++) {
        for (int j = 0; j < matrix[0].length; j++) {
            if(matrix[i][j] == target){
                return true;
            }
        }
    }
    return false;
}

// 解法2 二分查找
// 每一行都是有序的，对每一行都进行二分查找
public boolean findNumberIn2DArray(int[][] matrix, int target) {
	if(matrix == null || matrix.length == 0){
        return false;
    }
    for(int[] nums : matrix){
        if(binSearch4(nums,0,nums.length,target) != -1){
            return true;
        }
    }
    return false;
}
private int binSearch4(int[] nums,int start,int end,int target){
    
    int left = start,right = end;
    while(left <= right){
        int mid = (left + right) >> 1;
        if(mid < target){
            left = mid + 1;
        }else if(mid > target){
            right = mid - 1;
        }else{
            return mid;
        }
    }
    return -1;
}

// 解法3 z字型查找
// 利用给出的二维数组的性质，从右上角开始查找，如果目标值小于当前值，就说明目标值在左边，向左移动，如果大于，则说明目标值在下面，向下移动
public boolean findNumberIn2DArray(int[][] matrix, int target) {
    if(matrix == null || matrix.length == 0 || matrix[0].length == 0){
        return false;
    }
    int x = 0, y = matrix[0].length - 1;
    while(x < matrix.length && y >= 0){
        if(matrix[x][y] == target){
            return true;
        }
        else if(matrix[x][y] > target){
            y--;
        }
        else if(matrix[x][y] < target){
            x++;
        }
    }
    return false;
}

```



## 5.替换空格

直接使用StringBuilder，遍历字符串，根据规则进行替换

```java
public String replaceSpace(String s) {
    char[] str = s.toCharArray();
    StringBuilder res = new StringBuilder();
    for(int i=0;i<str.length;i++){
        if(str[i] != ' '){
            res.append(str[i]);
        }else{
            res.append("%20");
        }

    }
    return res.toString();
}
```



## 6.从尾到头打印链表

本质上就是链表的反转。

链表反转方法：迭代，递归，利用辅助栈

```java
//剑指 Offer 6. 从尾到头打印链表
public int[] reversePrint(ListNode head) {
    head = reverseList(head);
    ListNode p = head;
    List<Integer> list = new ArrayList();
    while (p != null) {
        list.add(p.val);
        p = p.next;
    }
    return list.stream().mapToInt(value -> value).toArray();
}

private ListNode reverseList6(ListNode node) {
    //新的头结点
    if (node.next == null) {
        return node;
    }
    //反转后面的链表
    ListNode head = reverseList(node.next);
    node.next.next = node;
    node.next = null;
    return head;
}
```



## 7.重建二叉树

模拟通过前序遍历和中序遍历构造二叉树的过程。

每一次构造的时候都需要指定当前子树的根节点所处的位置，以及当前子树得到范围，即在中序遍历中的开始位置和结束位置。

```java
public TreeNode buildTree(int[] preorder, int[] inorder) {
    Map<Integer,Integer> preMap = new HashMap<>(), inMap = new HashMap<>();
    for (int i = 0; i < preorder.length; i++) {
        preMap.put(preorder[i],i);
        inMap.put(inorder[i],i);
    }
    int rootNum = preorder[0];
    int rootInIndex = inMap.get(rootNum);
    TreeNode root = new TreeNode(rootNum);
    int leftLen = inMap.get(rootNum) - 0;
    int rightLen = inorder.length - inMap.get(rootNum) - 1;
    root.left = leftLen == 0 ? null : buildTreeHelper(preorder,inorder,1,0,rootInIndex-1,preMap,inMap);
    root.right = rightLen == 0 ? null : buildTreeHelper(preorder,inorder,0 + leftLen + 1,rootInIndex+1,inorder.length,preMap,inMap);
    return root;
}

public TreeNode buildTreeHelper(int[] preorder,int[] inorder,int rootPreIndex,int start,int end,Map<Integer,Integer> preMap,Map<Integer,Integer> inMap){
    int rootNum = preorder[rootPreIndex];
    TreeNode node = new TreeNode(rootNum);
    int rootInIndex = inMap.get(rootNum);
    int leftStart = start;
    int leftEnd = rootInIndex - 1;
    int leftRootPreIndex = rootPreIndex + 1;
    if(leftStart > leftEnd || leftRootPreIndex >= preorder.length){
        node.left = null;
    }else{
        node.left = buildTreeHelper2(preorder,inorder,leftRootPreIndex,leftStart,leftEnd,preMap,inMap);
    }
    int rightStart = rootInIndex + 1;
    int rightEnd = end;
    int rightRootPreIndex = rootPreIndex + (leftEnd - leftStart + 1) + 1;
    if(rightStart > rightEnd || rightRootPreIndex >= preorder.length){
        node.right = null;
    }else{
        node.right = buildTreeHelper2(preorder, inorder, rightRootPreIndex, rightStart, rightEnd, preMap, inMap);
    }
    return node;
}
```

## 9.用两个栈实现队列

一个栈用来入队，一个栈用来出队。

入队栈直接添加元素就行。出队栈当栈中没有元素的时候，就将入队栈内的所有元素转移到出队栈中。

```java
//9.用两个栈实现队列
class CQueue {
    //输入栈
    Deque<Integer> in;
    //输出栈
    Deque<Integer> out;

    public CQueue() {
        in = new ArrayDeque<>();
        out = new ArrayDeque<>();
    }

    public void appendTail(int value) {
        in.push(value);
    }

    public int deleteHead() {
        //如果当前输出栈为空，就将输入栈中所有的数据移动到输出栈中
        if (out.size() == 0) {
            in2out();
        }
        return out.size() == 0 ? -1 : out.pop();
    }

    //输入栈数据移动到输出栈
    private void in2out() {
        while (in.size() != 0) {
            out.push(in.pop());
        }
    }
}
```



## 10-Ⅰ.斐波那契数列，10-Ⅱ.青蛙跳台阶问题

三个变量，一个是F(n-2),一个是F(n-1),一个是F(n)，根据规则计算即可。

注意计算F(n)的时候需要取模

```java
public int fib2(int n) {
    if (n < 2) {
        return n;
    }
    int p = 0, q = 0, r = 1;
    for (int i = 2; i <= n; ++i) {
        p = q;
        q = r;
        r = (p + q) % MOD;
    }
    return r;
}
```





## 11.旋转数组的最小数字

二分的中间值nums[mid]与最右侧nums[right]的数据比较，

如果小于右侧，说明目标值在mid的左侧或者就等于mid，所以忽略mid右侧的部分,right = mid

如果大于右侧，说明目标值在mid的右侧，忽略mid左侧的部分，left = mid + 1

如果相等，就让right左移一位

```java
//剑指 Offer 11. 旋转数组的最小数字
public int minArray(int[] numbers) {
    int left = 0, right = numbers.length - 1;
    while(left < right){
        int mid = (right - left) / 2 + left;
        if(numbers[mid] > numbers[right]){
            // 中间值大于最右侧元素，说明中间值在目标值左侧
            left = mid + 1;
        }else if(numbers[mid] < numbers[right]){
            right = mid;
        }else{
            right--;
        }
    }
    return numbers[left];
}
```



## 12.矩阵中的路径

```java
//剑指 Offer 12. 矩阵中的路径
//回溯法
private int[][] move = new int[][]{{-1,0},{0,-1},{1,0},{0,1}};

public boolean exist(char[][] board, String word) {
    int m = board.length, n = board[0].length;
    boolean[][] visited = new boolean[m][n];
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if(board[i][j] == word.charAt(0) && dfsHelperFor12(board,i,j,1,word,visited)){
                return true;
            }
        }
    }
    return false;
}

// 以x，y中中心，向四周移动，寻找word.charAt(index) == board[newX][newY] 的newX,newY,同时注意visited不能被访问
public boolean dfsHelperFor12(char[][] board,int x,int y,int index,String word,boolean[][] visited){
    // 递归结束标志
    if(index == word.length()){
        return true;
    }
    // 标记当前点已经被访问
    visited[x][y] = true;
    int m = board.length, n = board[0].length;
    for (int i = 0; i < move.length; i++) {
        int newX = x + move[i][0], newY = y + move[i][1];
        if(check(newX,newY,m,n) && board[newX][newY] == word.charAt(index) && !visited[newX][newY]){
            // 递归
            if(dfsHelperFor12(board,newX,newY,index+1,word,visited)){
                return true;
            }
        }
    }
    // 点x,y向四周移动均无法满足要求，该点不能使用，回溯撤销访问标志
    visited[x][y] = false;
    return false;
}

private boolean check(int i,int j,int m,int n){
    return i>=0 && i < m && j>=0 && j<n;
}
```



## 13.机器人的运动范围

深度有限搜索，只需要对一个位置是否可以访问进行标记即可。需要注意一个点是否可以访问的条件判断。



```java
// 剑指 Offer 13. 机器人的运动范围
private int[][] move = new int[][]{{-1,0},{0,-1},{1,0},{0,1}};

private int path = 1;

public int movingCount(int m, int n, int k) {
    // 0初始情况，1可以访问，2无法访问
    int[][] flags = new int[m][n];
    // 0，0 初始位置一定可以访问
    flags[0][0] = 1;
    dfsHelperFor13(m,n,0,0,k,flags);
    return path;
}

public void dfsHelperFor13(int m,int n,int x,int y,int k,int[][] flags){
    for (int i = 0; i < move.length; i++) {
        int newX = x + move[i][0], newY = y + move[i][1];
        // 遇到一个没有访问过的位置，需要判断这个位置是否可以访问
        if(check(newX,newY,m,n) && flags[newX][newY] == 0){
            // 满足条件可以访问
            if(calculateNum(newX) + calculateNum(newY) <= k){
                flags[newX][newY] = 1;
                path++;
                // 继续深入探索
                dfsHelperFor13(m,n,newX,newY,k,flags);
            }else{
                flags[newX][newY] = 2;
            }
        }
    }
}

private boolean check(int i,int j,int m,int n){
    return i>=0 && i < m && j>=0 && j<n;
}

// 计算一个数字的各个数位之和
public int calculateNum(int i){
    int res = 0;
    while(i != 0){
        res+=i%10;
        i/=10;
    }
    return res;
}
```



## 14.剪绳子

类似于背包问题

```java
// 剑指 Offer 14- I. 剪绳子
public int cuttingRope(int n) {
    if(n <= 1){
        return n;
    }
    // dp[i] 表示长度为i的绳子可以达到的最大乘积
    int[] dp = new int[n + 1];
    dp[1] = 1;
    dp[2] = 1;
    // 遍历每一个长度的绳子
    for (int i = 3; i <= n; i++) {
        // 将绳子分成长度为j、i-j两端，那么当前的分法可能的最大乘积就是max(j*(i-j),j*dp[i-j])
        for (int j = 1; j <= i/2; j++) {
            dp[i] = Math.max(dp[i],Math.max(j*(i-j),j*dp[i-j]));
        }
    }
    return dp[n];
}
```



## 15. 二进制中1的个数

```java
//剑指 Offer 15. 二进制中1的个数
//循环检查每一位  将带检查的位置为1，其他位置为0，将该数与n进行与运算，结果为0，该位置为0否则该位置为1
public int hammingWeight(int n) {
    int res = 0;
    for (int i = 0; i < 32; i++) {
        if (((1 << i) & n) != 0) {
            res++;
        }
    }
    return res;
}
```



## 16. 数值的整数次方

```java
//剑指 Offer 16. 数值的整数次方
public double myPow(double x, int n) {
    long N = n;
    return n >= 0 ? quickMul(x,N): 1.0 / quickMul(x,-N);
}
private double quickMul(double x, long N){
    double res = 1.0;
    double x1 = x;
    while(N > 0){
        // N最终都会变成1，这里就是将幂的结果放入res中
        // 另外如果N为奇数，那么会先乘一次x，这样最后计算的时候就等同于幂上+1
        if(N % 2 == 1){
            res*=x1;
        }
        // 之后计算幂都是2次幂进行的，N也要除2
        x1*=x1;
        N/=2;
    }
    return res;
}
```





## 17. 打印从1到最大的n位数

```java
//剑指 Offer 17. 打印从1到最大的n位数
public int[] printNumbers(int n) {
    int len = (int) Math.pow(10, n) - 1;
    int[] res = new int[len];
    for (int i = 0; i < len; i++) {
        res[i] = i + 1;
    }
    return res;
}
```

上面的做法比较简答，其实有更加通用性的做法，当n比较大的时候也可以打印。

横向循环，纵向递归，从1开始填，递归填写后一位的数字（循环0~9），填完加入返回列表中

```java
//其实这道题主要考查的是大数打印
private char[] number = new char[]{'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'};

public List<String> printNumbers(int n) {
    List<String> list = new ArrayList<>();
    //先填第一位数字  首位数字不能为0
    for (int i = 1; i < 10; i++) {
        String str = new String(String.valueOf(number[i]));
        System.out.println(str);
        list.add(str);
        dfsForPrintNumbers(list, str, 1, n);
    }
    return list;

}

private void dfsForPrintNumbers(List<String> list, String oldNum, int cur, int n) {
    if (cur == n)
        return;
    for (int i = 0; i < 10; i++) {
        String newNum = oldNum + number[i];
        System.out.println(newNum);
        list.add(newNum);
        dfsForPrintNumbers(list, newNum, cur + 1, n);
    }
}
```

## 18. 删除链表的节点

```java
//剑指 Offer 18. 删除链表的节点
public ListNode deleteNode(ListNode head, int val) {
    // 定义一个虚拟头节点
    ListNode newHead = new ListNode(0,head);
    // 指针
    ListNode point = newHead;
    while(point.next != null && point.next.val != val){
        point = point.next;
    }
    // 结束循环 point.next == null 或者   point.next.val == val 找到目标节点
    if(point.next == null){
        return newHead.next;
    }
    // point.next就是目标节点
    point.next = point.next.next;
    return newHead.next;
}
```





## 21. 调整数组顺序使奇数位于偶数前面

```java
//剑指 Offer 21. 调整数组顺序使奇数位于偶数前面
public int[] exchange(int[] nums) {
    /**
         * 原地交换
         * 双指针，left从左边找到第一个偶数的位置，right从右边找到第一个奇数的位置，然后进行交换即可
         */
    int len = nums.length;
    int left = 0, right = len - 1;
    while (left < right) {
        //从左边找到第一个偶数的位置
        while (left < right && nums[left] % 2 != 0) {
            left++;
        }
        //从右边找到第一个奇数的位置
        while (left < right && nums[right] % 2 == 0) {
            right--;
        }
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
    }
    return nums;
}
```



## 22.链表中倒数第k个节点

```java
public ListNode getKthFromEnd(ListNode head, int k) {
    ListNode quick = head, slow = head;
    while(k != 0){
        quick = quick.next;
        k--;
    }
    while(quick != null){
        quick = quick.next;
        slow = slow.next;
    }
    return slow;

}
```



## 24.反转链表

```java
// 方法1 递归
public ListNode reverseList(ListNode head) {
    if(head == null || head.next == null){
        return head;
    }
    // 反转之后新的头节点
    ListNode newHead = reverseList(head.next);
    // 当前节点的后继节点的next指向当前节点
    head.next.next = head;
    head.next = null;
    return newHead;
}

// 方法2 迭代
public static ListNode reverseList(ListNode head) {
    if(head == null || head.next == null){
        return head;
    }
    ListNode cur = head;
    ListNode pre = null, after = null;
    while(cur != null){

        // 保存下一个节点
        after = cur.next;
        // 当前节点的next指向前一个节点
        cur.next = pre;
        // 移动指针
        pre = cur;
        cur = after;

    }
    //head.next = null;
    return pre;
}
```



## 25.合并两个有序链表

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    // 先判空
    if(l1 == null){
        return l2;
    }
    if(l2 == null){
        return l1;
    }
    // 小的数字在前面
    if(l1.val <= l2.val){
        l1.next = mergeTwoLists(l1.next,l2);
        return l1;
    }else{
        l2.next = mergeTwoLists(l1,l2.next);
        return l2;
    }


}
```



## 26.树的子结构

比较简单，就是空值的情况题目没有说清楚，有点难以进行判断。

```java
//剑指 Offer 26. 树的子结构
//先序dfs
public boolean isSubStructure(TreeNode A, TreeNode B) {
    if(A == null || B == null){
        return false;
    }
    if(A.val == B.val){
        if(dfs26(A,B)){
            return true;
        }
    }
    return isSubStructure(A.left,B) || isSubStructure(A.right,B);
}
private boolean dfs26(TreeNode A,TreeNode B){
    if(B == null){
        return true;
    }
    if(A == null){
        return A == B;
    }
    if(A.val != B.val){
        return false;
    }
    return dfs26(A.left,B.left) && dfs26(A.right,B.right);
}
```



## 27.二叉树的镜像

```java
//27. 二叉树的镜像
public TreeNode mirrorTree(TreeNode root) {
    if(root == null){
        return null;
    }
    TreeNode temp = root.left;
    root.left = mirrorTree(root.right);
    root.right = mirrorTree(temp);
    return root;
}
```



## 28.对称的二叉树

```java
public boolean isSymmetric(TreeNode root) {
    if(root == null){
        return true;
    }
    return helper(root.left,root.right);
}

public boolean helper(TreeNode node1,TreeNode node2){
    if(node1 == null && node2 == null){
        return true;
    }
    if(node1 == null || node2 == null){
        return false;
    }
    return node1.val == node2.val && helper(node1.left,node2.right) && helper(node1.right,node2.left);

}
```



## 29.顺时针打印矩阵

```java
//剑指 Offer 29. 顺时针打印矩阵
//由外到内 每次打印一层
public int[] spiralOrder(int[][] matrix) {
    if (matrix == null || matrix.length == 0) {
        return new int[0];
    }
    int[] res = new int[matrix.length * matrix[0].length];
    int level = matrix.length % 2 == 0 ? matrix.length / 2 : matrix.length / 2 + 1;
    int index = 0;
    for (int i = 0; i < level; i++) {
        index = printNumsInOrder29(matrix, res, index, i, matrix.length - 1 - i, i, matrix[0].length - 1 - i);
    }
    return res;
}

//3x3  level=1
private int printNumsInOrder29(int[][] matrix, int[] res, int index, int top, int bottom, int left, int right) {
    //上
    for (int i = left; i <= right; i++) {
        res[index++] = matrix[top][i];
    }
    if (index >= res.length)
        return index;
    //右
    for (int i = top + 1; i <= bottom; i++) {
        res[index++] = matrix[i][right];
    }
    if (index >= res.length)
        return index;
    //下
    for (int i = right - 1; i >= left; i--) {
        res[index++] = matrix[bottom][i];
    }
    if (index >= res.length)
        return index;
    //左
    for (int i = bottom - 1; i >= top + 1; i--) {
        res[index++] = matrix[i][left];
    }
    if (index >= res.length)
        return index;
    return index;
}
```





## 30.包含min函数的栈

```java
class MinStack {

    private Deque<Integer> deque;
    private Deque<Integer> minQue;
    /** initialize your data structure here. */
    public MinStack() {
        deque = new LinkedList<>();
        minQue = new LinkedList<>();
        minQue.push(Integer.MAX_VALUE);
    }

    public void push(int x) {
        deque.push(x);
        minQue.push(Math.min(x,minQue.peek()));
    }

    public void pop() {
        deque.pop();
        minQue.pop();
    }

    public int top() {
        return deque.peek();
    }

    public int min() {
        return minQue.peek();
    }
}
```



## 31.栈的压入、弹出序列

```java
public boolean validateStackSequences(int[] pushed, int[] popped) {
    Deque<Integer> stack = new LinkedList<>();
    int n = pushed.length;
    for(int i=0, j=0;i<n;i++){
        // 入栈一个元素
        stack.push(pushed[i]);
        // 检查当前入栈的元素是否等于出栈序列的元素，如果等于，就执行出栈
        while(!stack.isEmpty() && stack.peek() == popped[j]){
            stack.pop();
            j++;
        }
    }
    return stack.isEmpty();
}
```



## 31-Ⅰ从上到下打印二叉树

```java
public int[] levelOrder(TreeNode root) {
    if(root == null){
        return new int[0];
    }
    List<Integer> list = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    while(queue.size() != 0){
        // 一层的个数
        int size = queue.size();
        while(size != 0){
            // 出队一个节点
            TreeNode node = queue.poll();
            list.add(node.val);
            // 左右子节点入队
            if(node.left != null) queue.add(node.left);
            if(node.right != null) queue.add(node.right);
            size--;
        }
    }
    int[] ans = new int[list.size()];
    for(int i=0;i<ans.length;i++){
        ans[i] = list.get(i);
    }
    return ans;
}
```



## 32-Ⅱ从上到下打印二叉树

其实也是二叉树的广度优先遍历，不过在遍历每一层的时候需要判断这一层是从左到右还是从右到左。加一个boolean值判断一下就行。

```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> ans = new LinkedList<List<Integer>>();
    if (root == null) {
        return ans;
    }

    Queue<TreeNode> nodeQueue = new ArrayDeque<TreeNode>();
    nodeQueue.offer(root);
    boolean isOrderLeft = true;

    while (!nodeQueue.isEmpty()) {
        Deque<Integer> levelList = new LinkedList<Integer>();
        int size = nodeQueue.size();
        for (int i = 0; i < size; ++i) {
            TreeNode curNode = nodeQueue.poll();
            if (isOrderLeft) {
                levelList.offerLast(curNode.val);
            } else {
                levelList.offerFirst(curNode.val);
            }
            if (curNode.left != null) {
                nodeQueue.offer(curNode.left);
            }
            if (curNode.right != null) {
                nodeQueue.offer(curNode.right);
            }
        }
        ans.add(new LinkedList<Integer>(levelList));
        isOrderLeft = !isOrderLeft;
    }

    return ans;
}

```



## 33.二叉搜索树的后续遍历序列

后续遍历序列中，范围内的最后一个点就是根节点，从根节点向前寻找，找到第一个小于根节点的位置index，可以将整个序列分为三个部分，start~index，左子树，index+1~end-1，右子树，end，根节点

寻找index的时候已经保证了右子树全部大于根节点，现在就要判断左子树是不是全部大于根节点。

遍历左子树，与根节点比较。

最后递归判断左子树和右子树是不是二叉搜索树。

```java
public boolean verifyPostorder(int[] postorder) {
    return verifyInRange(postorder,0, postorder.length-1);
}
private boolean verifyInRange(int[] postorder,int start,int end){
    if(start > end){
        return true;
    }
    //最后一个为根节点
    int root = postorder[end];
    int index = end-1;
    while(index >= start && postorder[index] > root){
        index--;
    }
    for(int i=start;i<=index;i++){
        if(postorder[i] >= root){
            return false;
        }
    }
    //找到左子树最后一个节点 [start,index] [index+1,end-1]
    return verifyInRange(postorder,start,index) && verifyInRange(postorder,index + 1,end-1);
}
```



## 34.二叉树中和为某一值的路径

题目限制了路径是从根节点到叶子节点的，所以相对简单一些。

需要的参数有：返回列表，路径，目标值以及树节点。

每一次判断等于目标值的时候，就需要将当前路径添加到返回列表中。要注意回溯，将新添加的数据从路径中移除。

没判断成功，就对左右子树进行求和判断。

```java
public List<List<Integer>> pathSum(TreeNode root, int target) {
    List<List<Integer>> res = new ArrayList<>();
    pathHelper(root,target,res,new LinkedList<>());
    return res;
}
public void pathHelper(TreeNode node,int target,List<List<Integer>> res, Deque<Integer> path){
    if(node == null)
        return;
    int val = node.val;
    // 叶子节点
    if(node.left == null && node.right == null){
        if(target == val){
            path.addLast(val);
            res.add(new ArrayList<>(path));
            path.removeLast();
            return;
        }
    }
    path.addLast(val);
    pathHelper(node.left,target-val,res,path);
    pathHelper(node.right,target-val,res,path);
    path.removeLast();
}
```



## 35.复杂链表的复制

TODO：



## 36.二叉搜索树与双向链表

将二叉搜索树的中序序列转换为双向链表。

所以在dfs的时候需要使用中序遍历的方式，先递归调用左子树，然后处理当前节点，最后递归调用右子树。

维护一个头指针，一个前驱指针。

处理当前节点的时候，如果pre为null，就认为是中序遍历的第一个节点，也就是链表的头节点，将head指向这个节点。

如果pre不为null，就需要将pre.right 指向 当前节点cur

然后将当前节点的left指向前驱pre，cur.left = pre

移动前驱指针，指向当前节点（当前节点是写一个处理的节点的前驱节点）

递归处理右子树

题解：

[剑指 Offer 36. 二叉搜索树与双向链表 - 力扣（LeetCode）](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/solutions/186518/mian-shi-ti-36-er-cha-sou-suo-shu-yu-shuang-xian-5/?envType=featured-list&envId=xb9nqhhg%3FenvType%3Dfeatured-list&envId=xb9nqhhg)

```java
Node pre, head;
public Node treeToDoublyList(Node root) {
    if(root == null){
        return root;
    }
    // dfs处理
    dfs(root);
    // 处理结果
    head.left = pre;
    pre.right = head;
    return head;
}

// 中序遍历
public void dfs(Node cur){
    if(cur == null){
        return;
    }
    dfs(cur.left);

    // 遇到第一个叶子节点，也就是链表的头节点
    if(pre == null){
        head = cur;
    }else{
        pre.right = cur;
        cur.left = pre;
    }
    // 更新pre为当前节点，当前节点是后继节点的前驱
    pre = cur;
    dfs(cur.right);

}
```

## 37.序列化二叉树

就是将二叉树以一种方式进行序列化，然后再反序列化。

序列化的时候可以利用层次遍历的方式，遇到null，就填写None字符串。



反序列话的时候比如根节点是index（从1开始计数），那么左子节点就是2 * index，右子节点就是2 * index+1。

这样可以递归构造。

```java
public class Codec {
    public String serialize(TreeNode root) {
        String str = rserialize(root, "");
        System.out.println(str);
        return str;
    }
  
    public TreeNode deserialize(String data) {
        String[] dataArray = data.split(",");
        List<String> dataList = new LinkedList<String>(Arrays.asList(dataArray));
        return rdeserialize(dataList);
    }

    public String rserialize(TreeNode root, String str) {
        if (root == null) {
            str += "None,";
        } else {
            str += str.valueOf(root.val) + ",";
            str = rserialize(root.left, str);
            str = rserialize(root.right, str);
        }
        return str;
    }
  
    public TreeNode rdeserialize(List<String> dataList) {
        if (dataList.get(0).equals("None")) {
            dataList.remove(0);
            return null;
        }
  
        TreeNode root = new TreeNode(Integer.valueOf(dataList.get(0)));
        dataList.remove(0);
        root.left = rdeserialize(dataList);
        root.right = rdeserialize(dataList);
    
        return root;
    }
}
```



# 代码随想录

## 单调栈

使用单调栈的题目通常为：给定一个一维数组，要寻找任意一个元素的右边或者左边第一个比自己大或者小的元素的位置，此时我们就要想到使用单调栈了。

思想就是空间换时间，使用一个栈来存储已经遍历的数据元素。

单调栈中存放的是元素的下标，取数的时候只需要得到下标再从数组中取数就可以了。

单调栈从栈顶到栈尾是有顺序的。
