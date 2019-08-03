# 算法
## 常见排序实现
### 冒牌排序
```
public static void sort01(int[] arr)
    {
        for(int i=arr.length-1;i>=0;i--)//外反
        {
            for(int j=0;j<i;j++)//内正(判断条件j<i)
            {
                if(arr[j]>arr[j+1])//交换元素j和j+1
                {
                    swap(arr,j,j+1);
                }
            }
        }
    }
```
#### 实现细节
两层循环，外反，内正(判断条件j<i)，交换元素j和j+1
### 插入排序
```
public static void sort02(int[] arr)
    {
        for(int i=1;i<arr.length;i++)
        {
            for(int j=i-1;j>-1&&arr[j]>arr[j+1];j--)
            {
                swap(arr,j,j+1);
            }
        }
    }
```
#### 实现细节
两层循环，外正内反，内存循环较复杂j=i-1，j>-1,arr[j]>arr[j+1],j--,j一直指向的是有序的部分
#### 和冒泡排序的比较
都是j和j+1之间的交换，
比冒泡少了一层if，将判断放入到for循环的条件中
冒泡是外反内正 j=0，j<i
插入是外正内反 j=i-1
### 选择排序
```
public static void sort03(int[] arr)
    {
        int minIndex=0;
        for(int i=0;i<arr.length;i++)
        {
            minIndex=i;
            for(int j=i+1;j<arr.length;j++)
            {
                minIndex=arr[minIndex]>arr[j]?j:minIndex;
            }
            swap(arr,i,minIndex);
        }
    }
```
#### 实现细节
两层都是正的，走内部循环之前minIndex指向当前i，内部循环找出i+1到最后的最小的那个，内存循环之后进行交换
两正，一个增量
### 堆排序
```
public static void sort(int[] arr)
    {
        for(int i=0;i<arr.length;i++)
        {
            heapInsert(arr,i);
        }
        int size=arr.length;
        swap(arr,0,--size);
        while(size>0)
        {
            heapify(arr,0,size);
            swap(arr,0,--size);
        }
    }
public static void heapify(int[] arr,int index,int size)
    {
         int left=index*2+1;
         while(left<size)
         {
             int largest=left+1<size&&arr[left+1]>arr[left]?left+1:left;
             largest = arr[largest] > arr[index] ? largest : index;

             if(largest==index)
             {
                 break;
             }
             swap(arr,largest,index);
             index=largest;
             left=index*2+1;
         }
    }
public static void heapInsert(int[] arr,int index)
    {
        while(arr[(index-1)/2]<arr[index])
        {
            swap(arr,(index-1)/2,index);
            index=(index-1)/2;
        }
    }
```
#### 实现细节
##### heapInsert实现细节
传入数组的同时传入index，以index为起点，向前倒，也就是两条语句，我们要形成大顶堆，三行代码，也就是index和（index-1）/2的关系
##### heapheapify实现细节
传入的参数，我们要传入size，size是下标，size开始以及之后的下标都是有序，size之前一直都是维持一个大顶堆，还有index，这个inde一直都是0.
判断条件就是left<size
最开始三个关系的判断，也就是index，left，right中我们找一个最大的，但是我们要时刻注意right是可能不存在的，只有在如下的条件才有可能去得到right
left+1<size这个保证了right是可以获取到的
在上面的基础上
arr[left+1]>arr[left]
我们就可以取right
##### 主逻辑实现细节
先是一个for循环heapInsert，
while(size>0)
{
      //调整 arr，0，size
      交换swap（arr,0,--size）;
}
### 快速排序
```
public static void swap(int[] arr,int m,int n)
    {
        int temp=arr[m];
        arr[m]=arr[n];
        arr[n]=temp;
    }

public static void sort03(int[] arr)
    {
        quickSort(arr,0,arr.length-1);
    }

public static void quickSort(int[] arr,int l,int r)
    {
        if(l<r)
        {
             swap(arr,r,l+(int)Math.random()*(r-l+1));
             int[] p=parition(arr,l,r);
             quickSort(arr,l,p[0]-1);
             quickSort(arr,p[1]+1,r);
        }
    }
public static int[] parition(int[] arr,int l,int r)
    {
        int less=l-1;
        int more=r;
        while(l<more)
        {
            if(arr[l]>arr[r])
            {
                swap(arr,--more,l);
            }
            else if(arr[l]<arr[r])
            {
                swap(arr,++less,l++);
            }
            else
            {
                l++;
            }
        }
        swap(arr,more,r);
        return new int[]{less+1,more};
    }
```
#### 实现细节
两个部分，一部分是递归，一部分是分割
首先我们在l---r中选择一个随机数
l+（r-l）*math.random()
分割的细节就是边界，最后返回的是less+1，more
我们递归的是l，p[0]-1
r,p[1]+1
### 参半排序
```
public static void mergeSort(int[] arr,int l,int r)
{
        if(l==r)
        {
            return;
        }
        int mid = l + ((r - l) >> 1);
        mergeSort(arr,l,mid);
        mergeSort(arr,mid+1,r);
        merge(arr,l,r,mid);
}
public static void merge(int[] arr,int l,int r,int mid)
{
        int index=0;
        int p1=l;
        int p2=mid+1;
        int[] help=new int[r-l+1];
        while(p1<mid+1&&p2<r+1)
        {
            help[index++]=arr[p1]<arr[p2]?arr[p1++]:arr[p2++];
        }
        while(p1<mid+1)
        {
            help[index++]=arr[p1++];
        }
        while(p2<r+1)
        {
            help[index++]=arr[p2++];
        }
        for(int i=0;i<=help.length-1;i++)
        {
            arr[l+i]=help[i];
        }
}
```
## 斐波那契数列及其变形
### 变形问题
跳台阶问题，生奶牛问题
### 递归解法
### 非递归解法，时间复杂度O(n)
#### 斐波那契和爬楼梯
```
 public static int f3(int n)
    {
        if(n==0) return 0;
        if(n==1||n==2) return 1;//base case可以修改
        int res=1;
        int pre=1;
        int tmp;
        for(int i=3;i<=n;i++)
        {
            tmp=res;
            res=pre+res;
            pre=tmp;
        }
        return res;
    }
```
#### 生奶牛问题
```
public static int c2(int n)
    {
        if(n==0) return 0;
        if(n==1||n==2||n==3) return n;
        int res=3;
        int pre=2;
        int prepre=1;
        int tmp1=0;
        int tmp2=0;
        for(int i=4;i<=n;i++)
        {
            tmp1=res;
            tmp2=pre;
            res=res+prepre;
            pre=tmp1;
            prepre=tmp2;
        }
        return res;
    }
```
#### 解法分析
在for循环中，先用tmp(临时变量)，存储一会需要用到的值，然后res=res+某个值，自己给自己改变，然后最后的临时变量赋给下次循环用的值
```
for(......)
{
   .......//给临时变量赋值
   res=res+....
   .......//赋值给临时变量
}
```
### 非递归解法，时间复杂度O(logn)
#### 两个矩阵相乘
```
public static int[][] mutliMatrix(int[][] m1,int[][] m2)
{
        int[][] res=new int[m1.length][m2[0].length];
        for(int i=0;i<m1.length;i++)
        {
            for(int j=0;j<m2[0].length;j++)
            {
                for(int k=0;k<m1[0].length;k++)
                {
                    res[i][j]+=m1[i][k]*m2[k][j];
                }
            }
        }
        return res;
}
```
#### 矩阵的次幂
```
public static int[][] m(int[][] m,int p)
{
        int[][] res=new int[m.length][m[0].length];
        for(int i=0;i<res.length;i++)
        {
            res[i][i]=1;
        }
        int[][] tmp=m;
        for(;p!=0;p>>>=1)
        {
            if((p&1)!=0)
            {
                res=mutliMatrix(tmp,res);
            }
            tmp=mutliMatrix(tmp,tmp);
        }
        return res;
}
```
#### 斐波那契
```
public static int f(int n)
    {
        if(n==0) return 0;
        if(n==1||n==2) return 1;
        int[][] m={{1,1},{1,0}};
        int[][] res = m(m, n - 1);
        return res[0][0]+res[0][1];
    }
```
#### 爬楼梯
```
public static int f(int n)
    {
        if(n==0) return 0;
        if(n==1||n==2) return 1;
        int[][] m={{1,1},{1,0}};
        int[][] res = m(m, n - 1);
        return 2res[0][0]+res[0][1];
    }
```
#### 生小牛

## 二进制中1的个数
```
public static int count1(int n)
    {
        int res=0;
        while(n!=0)
        {
            res+=n&1;
            n>>>=1;
        }
        return res;
    }
    public static int count2(int n)
    {
        int res=0;
        while(n!=0)
        {
            n=n&(n-1);
            res++;
        }
        return res;
    }
    public static int count3(int n)
    {
        int res=0;
        while(n!=0)
        {
            n-=n&(~n+1);
            res++;
        }
        return res;
    }
```
### 细节
count1需要走32次循环，count2和count3只需要走1的个数，然后就是比较重要的，两个操作
```
n&(n-1)获取到删除数字最左边的1之后的值
n&(~n+1)获取到数字最左边的1
```
### 拓展
```
    /*
    一条语句判断是否是2的次幂
     */
    public static boolean f1(int n)
    {
        return (n&(n-1))!=0;
    }
    /*
    n--->m需要转换多少位  
    */
    public static int f2(int n,int m)
    {
        int res=n^m;
        return count1(res);
    }
```
## 数值的整数次方
### 递归
```
 public static int f2(int base,int e)
    {
        if(e==0)
        {
            return 1;
        }
        if(e==1)
        {
            return base;
        }
        int res=f2(base,e>>1);
        res*=res;
        if((e&0x1)==1)
        {
            res*=base;
        }
        return res;
    }
```
#### 实现细节
两个base case分别表示幂次为0和1，然后就是递归，返回，每个递归函数中如果是奇数，就要乘上自己
### 非递归
```
public static int f(int num,int p)
    {
        int tmp=num;
        int res=1;
        for(;p!=0;p>>>=1)
        {
            if((p&1)!=0)
            {
               res=tmp*res;
            }
            tmp=tmp*tmp;
        }
        return res;
    }
```