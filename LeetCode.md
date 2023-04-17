# LeetCode

## Hot100

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



## TODO：5. 最长回文子串

### 暴力法



### 动态规划



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







## TODO：33. 搜索旋转排序数组





## TODO：34. 在排序数组中查找元素的第一个和最后一个位置





## TODO：39. 组合总和



## TODO：46. 全排列



## TODO：48. 旋转图像



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
