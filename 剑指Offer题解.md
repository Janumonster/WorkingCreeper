# 剑指Offer题解

> 非按序解答

### **1、二维数组中的查找**

**题目描述：**在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**思路：**观察二维数组的规律，横向不断增大，纵向不断增大，只要target比右上角的大，那么这一排就可以排除，若比较小那么这一列就可以排除。从右上角的值开始判断，开始不断的将最上和最后一列的数据排除，不断减少查找范围，减少不必要的遍历。

```java
public boolean Find(int target, int [][] array) {

    if (array == null || array.length == 0 || array[0].length == 0){
        return false;
    }

    int height = array.length;
    int width = array[0].length;

    int h = 0,s = width -1;
    while (s >= 0 && h < height){
        if (array[h][s] == target){
            return true;
        }else if (array[h][s] > target){
            s--;
        }else {
            h++;
        }
    }

    return false;
}
```



### **2、替换空格**

**题目描述**

请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

**思路：**使用一个字符串构造器，将原字符串中的元素复制到新的字符串中，当遇到空格时，替换为所要求的字符。

```
public String replaceSpace(StringBuffer str) {
    StringBuilder sb = new StringBuilder();
    for (int i = 0 ;i < str.length() ; i++){
        if (str.charAt(i) == ' '){
            sb.append("%20");
        }else {
            sb.append(str.charAt(i));
        }
    }
    return sb.toString();
}
```



### **3、从尾到头打印链表**

**题目描述**

输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。

**思路：**这只是其中一种办法，还可以使用栈进行逆序，使用递归进行添加。这题主要就是逆序。使用栈就是add，再pop就形成逆序。

```
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    if (listNode == null){
        return null;
    }
    ArrayList<Integer> list = new ArrayList<>();

    while (listNode!= null){
        list.add(listNode.val);
        listNode = listNode.next;
    }

    Collections.reverse(list);

    return list;
}
```

这里看一下递归：

```java
public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    
    ArrayList<Integer> list = new ArrayList<>();

    while (listNode!= null){
		list.addAll(printListFromTailToHead(listNode.next));
        listNode = listNode.next;
    }

    return list;
}
```



### **4、重建二叉树**

**题目描述**

输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

**思路：**这部分我自己也掌握的不好，想了很久，参考了别人的答案，主要就是要理解前序遍历和中续遍历的顺序，其中一个关键的点就是，前序遍历的第一个一定是根节点，这个根节点在中序遍历中，将数组分为前后两个部分，左边在根节点的左子树上，右边在根节点的右子树上。知道了这个，我们就可以开始递归了。

```java
private Map<Integer,Integer> index = new HashMap<>();

public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
    for (int i=0;i<in.length;i++){
        index.put(in[i],i);
    }
    return reConstructBinaryTree(pre,0,pre.length-1,0);
}

private TreeNode reConstructBinaryTree(int [] pre,int start,int end,int offset){
    if (start > end){
        return null;
    }
    TreeNode treeNode = new TreeNode(pre[start]);
    int pos = index.get(treeNode.val);
    int leftSize = pos - offset;
    treeNode.left = reConstructBinaryTree(pre,start+1,start+leftSize,offset);
    treeNode.right = reConstructBinaryTree(pre,start+leftSize+1,end,offset+leftSize+1);
    return treeNode;
}
```



### 5、用两个栈实现队列

**题目描述**

用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

**思路：**这题的思路比较简单，首先要明白栈是先进后出的，而队列是先进先出的，所以这里使用一个栈专门用于处理入队，就是说入队就是入这个栈，当我们要出队的时候就去看第二个栈，如果为空，那么就将栈1中的所有元素放入栈2，注意这里放进去顺序就放过来了，所以栈2直接pop就行了。

```java
Stack<Integer> stack1 = new Stack<Integer>();
Stack<Integer> stack2 = new Stack<Integer>();

public void push(int node) {
    stack1.add(node);
}

public int pop() {
    if (stack2.isEmpty()){
        while (!stack1.isEmpty()){
            stack2.add(stack1.pop());
        }
    }
    return stack2.pop();
}
```



### **6、旋转数组的最小数字**

**题目描述**

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

**思路：**应为不清楚调换了几个数据，可能为0个，所以采取遍历的手段，直接从第2个数据开始遍历，若出现后者比前者小（一定是小），则输出后者。若循环执行完毕，说明没有被调换，直接输出第一个数据。

```java
public int minNumberInRotateArray(int [] array) {
    if (array.length == 0){
        return 0;
    }
    int k = array[0];
    for(int i = 1;i<array.length;i++){
        if (array[i] < k){
            return array[i];
        }else {
            k = array[i];
        }
    }

    return array[0];
}
```



### 7、斐波那契数列

**题目描述**

大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。n<=39

**思路：**斐波那契数列是一个递推的数列，新元素由前两个元素的和组成，所以这个数列开头一定是0,1···,n；所以当查询的数据超过2时就可以使用一个递归来计算，应为前两个已经知道了，所以查询次数会减二。（以下的递归都可以使用一个循环代替！！！）

```java
public int Fibonacci(int n) {
    if (n >= 2){
        return getFibonacci(0,1,n-2);
    }
    return n;
    //	int i1 = 0,i2=1;
    //	int result = 0;
    //	for(int i = 0 ,i <= n-2;i++){
    //   	 result = i1+i2;
    //    	 i1 = i2;
    //   	 i2 = result;
    //	}
    //	return result;
}

private int getFibonacci(int i, int i1, int n) {
    if (n == 0){
        return i+i1;
    }else {
        return getFibonacci(i1,i+i1,n-1);
    }
}
```



### 8、跳台阶

**题目描述**

一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

**思路：**这题就是斐波那契数列的应用，但开始是1,2，而不是0,1，我们考虑实际情况，当只有一个台阶时，只有一种方法，当有2级台阶，有两种方法，当有三级台阶，有3种方法，多写几个，发现每多一级台阶，方法就是前两个数据的和，因为新台阶到前两个台阶的方法也是有两个，所以这就是一个斐波那契数列。

```java
public int JumpFloor(int target) {
    if (target > 2){
        return getWaysNumber(1,2,target-2);
    }
    return target;
    //	int i1 = 1,i2=2;
    //	int result = 0;
    //	for(int i = 0 ,i < n-2;i++){
    //   	 result = i1+i2;
    //    	 i1 = i2;
    //   	 i2 = result;
    //	}
    //	return result;
}

private int getWaysNumber(int i, int i1, int i2) {
    if (i2 == 0){
    	//这里的返回值不一样，因为这里的太阶数，不是下标了，而是小标+1,所以直接返回i2,不用计算
        return i1;
    }else {
        return getWaysNumber(i1,i+i1,i2-1);
    }
}
```



### 9、变态跳台阶

**题目描述**

一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

**思路：**千万不能被表象吓住，找一张纸，多写一些，会发现1-1,2-2,3-4,4-8,5-16···，这不是2的次方吗？

```java
/**
 * 1    1
 * 2    2
 * 3    4   1+2+1
 * 4    8   1+3+3+1
 * 5    16  1+4+6+4+1
 * ···
 * 我们把每次都拆开来看，每次分成一步完成，到n步完成，就会得到后面的算是，这是（x+y）的n-1次方的系数，令x=y=1;就是答案。java中没有次方运算，所以，递归走起。
 */

public int JumpFloorII(int target) {
    if (target == 1)return target;
    return getWaysNumberII(2,target-2);
   	//这里都不用判断是否是第一个
    //	int result = 1;
    //	for(int i = 0 ,i < target-1;i++){
    //   	 result *= 2 ;
    //	}
    //	return result;
}

private int getWaysNumberII(int i, int i1) {
    if (i1 == 0){
        return i;
    }else {
        return getWaysNumberII(i*2,i1-1);
    }

}
```



### 10、矩形覆盖

**题目描述**

我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

**思路：**依然是斐波那契数列，这里就不用递归了，直接循环。这题主要是要发现这是个斐波那契数列，千万别数错了！！！

```java
public int RectCover(int target) {
    if (target <= 2){
        return target;
    }
    int i1 = 1;
    int i2 =2;
    int result = 0;
    for (int i = 3; i <= target;i++){
        result = i1+i2;
        i1 = i2;
        i2 = result;
    }

    return result;
}
```



### 11、剪绳子

**题目描述：**

把一段绳子剪成多段，并且使得每一段的长度乘积最大。

n=2，return 1 （2 = 1+1）

n=10，return 36 (10 = 3+3+4)

**思路：**观察发现尽可能多的剪长度为3的绳子，并且不允许出现长度为1的绳子，就能是结果最大。若按3剪完之后还剩1，那就将3中取出一段和1再拆成2+2；采用贪心算法。

```java
public int integerBreak(int n){
    if(n<2){
    	//这是别人的答案，不清楚1为什么也是0
        return 0;
    }
    if(n == 2)return 1;
    if(n == 3)return 2;
    int timesOf3 = n/3;//统计3的个数
    if(n - 3 * timesOf3 == 1){
        timesOf3 --;
    }
    int timesOf2 = (n - 3 * timesOf3)/2;
    return (int)(Math.pow(3,timesOf3))*(int)(Math.pow(2.timesOf2));
}
```

我们还可以采用动态规划的思维，分别计算每个长度剪成的最优解，再将绳子，剪成两段，一段为自然数增长，当然不能等于绳子长度，分别计算直接相乘，以及乘以另一段的最优解（通过之前的计算记录下来的），在和自身已经计算的值进行对比，得出最大值。

```java
public int integerBreak(int n){
    int[] sol = new Int[n+1];
    sol[1] = 1;
    for(int i = 2 ; i <= n ; i ++){
        for(int j = 1;j<i;j++){
            sol[i] = Math.max(sol[i],Math.max(j*(i-j),sol[j]*(i-j)))
        }
    }
    return sol[n];
}
```



### 12、二进制中1的个数

**题目描述**

输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。

**思路：**这题我之前是没有思路的，找了java中能将int转为二进制的方法，得到字符串，正数还行，负数就变成了32位。之后想不出来只能求助题解。发现java中有一个`Integer.bitCount(int n)`就是用来统计整数中1 的个数的，直接使用即可；之后又看了题解中的另一种解法，是采用n&(n-1)的方法来统计的。

```java
 public int NumberOf1(int n) {
     return Integer.bitCount(n);
     
     //		int cnt = 0；
     //		while(n!=0){
     // 	   cnt++;
     //  	   n &= (n-1);
     //		}
     //		return cnt；
 }
```



### 13、数值的整数次方

**题目描述**

给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

**思路：**这题其实可以用Math中的pow方法直接解决，所以这里提供另一种思路，如果按正常的思路是直接循环相乘，这样时间复杂度是O(N)，显然不是最优。当n%2==0时，x的n次方 = (x\*x)的n/2次方，当n%2 == 1时，x的n次方 =x\* (x\*x)的n/2次方，这样我们可以把时间复杂度缩小到O(logN)。

```java
 public double Power(double base, int exponent) {

        if (exponent == 0) return 1;
        if (exponent == 1) return base;
        boolean isNegative = false;
        if (exponent < 0){
            exponent = -exponent;
            isNegative = true;
        }
        double pow = Power(base*base ,exponent/2);
        if (exponent%2 != 0){
            pow = pow*base;
        }
        
        return isNegative ? 1/pow:pow;
    }
```



### 14、调整数组顺序使奇数位于偶数前面

**题目描述**

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。

**思路：**这题我没啥思路，为了保证相对位置，我就将奇数偶数分别按序存入两外两个数组，之后在对原数组进行赋值。虽然时间复杂度上无法改进，但空间复杂度还可以优化，第一遍遍历得出奇数的个数，再将原数组clone一个，直接对原数组进行修改。

```java
public void reOrderArray(int [] array) {
    int[] odd = new int[array.length];
    int[] even = new int[array.length];
    int l = 0,r = 0;
    for (int i = 0;i <array.length;i++){
        if (array[i]%2 == 0){
            even[r]=array[i];
            r++;
        }else {
            odd[l]=array[i];
            l++;
        }
    }
    for (int j = 0;j < l;j++){
        array[j] = odd[j];
    }
    for (int k = l;k<array.length;k++){
        array[k] = even[k-l];
    }
}
```



### 15、链表中的倒数第k个节点

**题目描述**

输入一个链表，输出该链表中倒数第k个结点。

**思路：**因为不知道链表的长度，所以只能想去尝试是否有k那么长，所以首先确定满足k长度，否则返回null，在满足长度的时候，将结果指向头指针，在遍历指针还没到尾部的时候，和遍历指正一起向后移动，当遍历指针到达尾部，结果指针所指即为结果。

```java
public ListNode FindKthToTail(ListNode head,int k) {
    	if (k == 0)return null;
        ListNode node = head;
        ListNode Kth = null;
        int m = 0;
        while (node != null){
            m++;
            node = node.next;
            if (m == k)Kth = head;
            if (m > k){
                Kth = Kth.next;
            }
        }
        return Kth;
}
```



### 16、反转链表

**题目描述**

输入一个链表，反转链表后，输出新链表的表头。

**思路：**另给两个辅助节点，由于保存交换带来的链表断开，之后不断修改next的方向就可以了，要注意判空，还要注意最后要将最后一个节点连接上。

```java
public ListNode ReverseList(ListNode head) {

    if (head == null)return null;

    ListNode left = null;
    ListNode right = head.next;

    while (right != null){
        head.next = left;
        left = head;
        head = right;
        right = head.next;
    }
    head.next = left;
    return head;
}
```



### 17、合并两个排序的链表

**题目描述**

输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。

**思路：**首先选取较小的那个链表的head为结果的head，之后不断去比较大小添加到head后面，当其中某一个链表结束时，直接将另一个链表中的所有数据加到末尾。

```java
public ListNode Merge(ListNode list1,ListNode list2) {

    if (list1 == null || list2 == null){
        return list1 == null ? list2 : list1;
    }

    ListNode head = null;
    if (list1.val <= list2.val){
        head = list1;
        list1 = head.next;
    }else {
        head = list2;
        list2 = head.next;
    }
    ListNode p = head;

    while (list1 != null&&list2 != null){
        if (list1.val <= list2.val){
            p.next = list1;
            p = list1;
            list1 = p.next;
        }else {
            p.next = list2;
            p = list2;
            list2 = p.next;
        }
    }

    if (list1 == null){
        p.next = list2;
    }else {
        p.next = list1;
    }

    return head;
}
```



### 18、数组中出现次数超过一半的数字

**题目描述**

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

**思路：**还用一个hash表来统计每个数字出现的次数，但出现超过一半就返回这个值。记得考虑边界情况，当长度为1时，单独考虑。

```java
public int MoreThanHalfNum_Solution(int [] array) {
    if (array.length == 1){
        return array[0];
    }
    Map<Integer,Integer> count = new HashMap<>();
    for (int i = 0;i<array.length;i++){
        if (count.get(array[i]) == null){
            count.put(array[i],1);
        }else {
            int k = count.get(array[i]);
            if (++k > array.length/2){
                return array[i];
            }else {
                count.put(array[i],k);
            }
        }
    }
    return 0;
}
```



### 19、树的子结构

**题目描述**

输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）

**思路：**首先我们要先找到A中与B的根节点相同的节点，之后再根据这个节点开始向下判断是否相同。我这里用了比较多的递归调用，第一层是为了找到所有A中满足和B根节点相等的点知道找到子结构为止，如果全部遍历还未找到，那就是没有。第二层递归是根据B的结构，去A中找是否相同。

```java
public boolean HasSubtree(TreeNode root1,TreeNode root2) {

    if (root1 == null)return false;

    if (root2 == null)return false;

    if (root1.val == root2.val){
        if (isFindTree(root1,root2)){
            return true;
        }
    }

    return HasSubtree(root1.left,root2)||HasSubtree(root1.right,root2);

}

public boolean isFindTree(TreeNode p, TreeNode q) {

    if ((p==null)&&(q!=null)){
        return false;
    }else if(q == null){
        return true;
    }

    if (p.val == q.val){
        return isFindTree(p.left,q.left)&& isFindTree(p.right,q.right);
    }else {
        return false;
    }
}
```



### 20、二叉树的镜像

**题目描述**

操作给定的二叉树，将其变换为源二叉树的镜像。

**输入描述**:

```
二叉树的镜像定义：源二叉树 
    	    8
    	   /  \
    	  6   10
    	 / \  / \
    	5  7 9 11
    	镜像二叉树
    	    8
    	   /  \
    	  10   6
    	 / \  / \
    	11 9 7  5
```

**思路：**对每个非空节点，将其左右互换，然后就是便利一遍。

```java
public void Mirror(TreeNode root) {
   if (root != null){
       TreeNode treeNode = root.left;
       root.left = root.right;
       root.right = treeNode;

       Mirror(root.left);
       Mirror(root.right);
   }
}
```



### 21、求1+2+3+...+n

**题目描述**

求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

**思路：**因为不能用哪些语句，所以首先想到递归，但是没有if怎么判断结束？之前我也没想到，看完题解焕然大悟，&&有一个比较好玩的用法，如果左边为假，那么不会执行右边，利用这个我们改一改就行了。

```java
public int Sum_Solution(int n) {
    int sum = n;
    boolean b = (n>0)&&((sum += Sum_Solution(n-1))>0);
    return sum;
}
```



### 22、顺时针打印矩阵

**题目描述**

输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.

**思路：**首先我们知道，矩形有四条边，而我们顺时针输出，就是要不断沿着这些边去输出。所以可以先给定四个边的位置，再一层一层输出。

```java
public ArrayList<Integer> printMatrix(int [][] matrix) {
    ArrayList<Integer> list = new ArrayList<>();

    int w1 = 0,w2 = matrix[0].length-1;
    int h1 = 0,h2 = matrix.length-1;

    while (w1 <= w2 && h1 <= h2){
        for (int i = w1;i<=w2;i++){
            list.add(matrix[h1][i]);
        }
        for (int j = h1+1;j <= h2;j++){
            list.add(matrix[j][w2]);
        }
        //这里两个判断不等，是为了防止单列和单行重复输出的情况
        if (h1!=h2){
            for (int k = w2 -1;k >= w1;k--){
                list.add(matrix[h2][k]);
            }
        }

        if (w1!=w2){
            for (int m = h2 -1;m > h1;m--){
                list.add(matrix[m][w1]);
            }
        }

        w1++;w2--;h1++;h2--;
    }
    return list;
}
```



### 23、包含min函数的栈

**题目描述**

定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。

**思路：**此题主要难点在于时间复杂度被限制为O(1)，说明我们需要实时更新这个最小值，以保证可以一步就取出来，我这里也用栈实现，当要加入元素时，比较一下将要加入的元素和最小值的大小，如果没有那就将最小值压入保存最小值的栈，只要有pop指令，两个栈都pop，最小值栈的栈顶就是目前栈的最小值。

```java
private Stack<Integer> stack = new Stack<>();
private Stack<Integer> minStack = new Stack<>();
private int min = Integer.MAX_VALUE;

public void push(int node) {
    if (node <= min){
        min = node;
    }
    stack.push(node);
    minStack.push(min);
}

public void pop() {
   stack.pop();
   minStack.pop();
}
//题目给出了这个函数，不知道怎么用
public int top() {
   return stack.peek();
}

public int min() {
   return minStack.peek();
}
```



### 24、栈的压入、弹出序列

**题目描述**

输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

**思路：**栈的弹出序列可有很多，无法通过固定的计算得出，所以我就选择进行一次模拟，每次压入后就进行一次比较，将栈顶和弹出序列作比较，相等继续弹出，弹出序列往后移；不等就放回，继续压入。最后比较是否完整遍历弹出序列即可。

```java
public boolean IsPopOrder(int [] pushA,int [] popA) {
     Stack<Integer> stack = new Stack<>();
        int j = 0,i = 0;
        stack.push(pushA[i++]);
        while (j<popA.length){
          if (popA[j] == stack.peek()){
              stack.pop();
              j++;
          }else {
              if (i == pushA.length)return false;
              stack.push(pushA[i++]);
          }
        }
        return true;
}
```



### 25、从上往下打印二叉树

**题目描述**

从上往下打印出二叉树的每个节点，同层节点从左至右打印。

**思路：**这题起初我没啥思路，想过递归发现无法实现，最后想到了队列，在我添加父节点的时候，按顺序间子节点添加到队列中，之后不断的更新队列，知道队列都为空。

```java
public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
    Queue<TreeNode> buff = new LinkedList<>();
    ArrayList<Integer> list = new ArrayList<>();
    buff.add(root);
    while (!buff.isEmpty()){
        int size = buff.size();
        while (size-- > 0){
            TreeNode t = buff.poll();
            if (t == null)continue;
            list.add(t.val);
            buff.add(t.left);
            buff.add(t.right);
        }
    }

    return list;
}
```



### 26、二叉搜索树的后序遍历序列

**题目描述**

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

**思路：**我们可以先写一个二叉搜索树找一找他的后序遍历的规律，发现当出现第一个大于根节点的时候，后面都必须大于根节点，而根节点就是我们的数组最后一个。

```java
public boolean VerifySquenceOfBST(int [] sequence) {
    if (sequence == null || sequence.length == 0){
        return false;
    }
    return checkTheOrder(sequence,0,sequence.length-1);
}

private boolean checkTheOrder(int[] sequence,int start,int end){
    if (end - start <= 1){
        return true;
    }
    int root = sequence[end];
    int mid = start;
    while (mid < end && sequence[mid] < root){
        mid++;
    }
    for (int i = mid;i<end;i++){
        if (sequence[i]<root){
            return false;
        }
    }
    return checkTheOrder(sequence,start,mid-1)&&checkTheOrder(sequence,mid,end-1);
}
```



### 27、二叉树中和为某一值的路径

**题目描述**

输入一颗二叉树的跟节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)

**思路：**这题就是考查数的遍历，只不过在遍历过程中去做一些判断，这题要注意的是，因为我用的是java，所以我的变量都是强引用的，在递归过程中要注意不要改变上一层的值或者list。

```java
ArrayList<ArrayList<Integer>> retList = new ArrayList<>();
ArrayList<Integer> pathList = new ArrayList<>();

public ArrayList<ArrayList<Integer>> FindPath(TreeNode root, int target) {
    if (root == null)return retList;

    pathList.add(root.val);
    target -= root.val;
    if (target == 0&&root.left == null&& root.right == null){
        retList.add(new ArrayList<>(pathList));
    }
    if (target > 0){
      FindPath(root.left,target);
      FindPath(root.right,target);
    }
    //前面直接添加进路劲，在都不符合条件时记得remove
    pathList.remove(pathList.size()-1);
    return retList;
}
```



### 28、复杂链表的复制

**题目描述**

输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

**思路：**这题我没有啥思路，凭我现在的实力还想不出解决办法，只能求助题解，说一说题解的思路。为了记住每个random指向的位置，我们借助于原链表，先将链表逐一复制，并插入到原链表的位置之后，这样第二遍就可以为random赋值了，而每个值都是在原random之后；最后再将链表拆开返回。

```java
public RandomListNode Clone(RandomListNode pHead){
    if (pHead == null)return null;
    RandomListNode cur = pHead;
    while (cur != null){
        RandomListNode clone = new RandomListNode(cur.label);
        clone.next = cur.next;
        cur.next = clone;
        cur = clone.next;
    }
    cur = pHead;
    while (cur != null){
       RandomListNode clone = cur.next;
       if (cur.random!=null){
           clone.random = cur.random.next;
       }
       cur = clone.next;
    }
    cur = pHead;
    RandomListNode retHead = pHead.next;
    while (cur.next != null){
        RandomListNode node = cur.next;
        cur.next = node.next;
        cur = node;
    }
    return retHead;
}
```

