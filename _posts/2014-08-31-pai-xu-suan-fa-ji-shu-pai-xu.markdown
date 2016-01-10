---
layout: post
title: "排序算法 - 计数排序"
date: 2014-08-31 22:02
comments: true
categories: algorithm
---

> 计数排序（Counting sort）是一个非基于比较的排序算法，该算法于1954年由 Harold H. Seward 提出。它的优势在于在对一定范围内的整数排序时，它的复杂度为Ο(n+k)（其中k是整数的范围），快于任何比较排序算法

计数排序原理简单的说，通过一个额外的统计数组C对目标数列A中的元素i个数进行统计，然后将目标数组A[n-1]根据统计数C中的次数反相填充。
具体步骤如下：

* 找到目标数组A中的最大和最小元素
* 创建统计数组C, C的长度为A(max)-A(min) + 1, C的初始值是0
* 对A中元素进行计数统计，统计结果放在C中对应的位置
* 将A中元素根据C中的顺序和次数依次输出

例如，数组A为[2,8,7,1,1,5,7]  
1.找出A的最小值为1， 最大值为8。   
2.创建数组C, 长度为(8-1) + 1 = 8, C: [0, 0, 0, 0, 0, 0, 0, 0], 注意C每个元素表示由min到max中元素的个数，这里是1到8中每个数出现的个数。   
3.统计A中，1到8之间数的累计计数，放到C中，那么C应该为：C[2, 1, 0, 0, 1, 0, 2, 1], 即：1累计2次，2累计1次，3累计0次， 4累计0次。。。。。   
4.将C中统计的A元素次数，重新输出即可，A2: [1, 1, 2, 5, 7, 7, 8]。    

Ruby代码实现如下：

```ruby
class Array
  def counting_sort
    min,max = self.minmax
    counter_array = Array.new(max-min+1,0)
    self.each { |element| counter_array[element-min] += 1 }
    sorted = []
    counter_array.each_with_index { |item, index| item.times {sorted << index + min} }
    sorted
  end
end
```
运算结果：[2,8,7,1,1,5,7].counting_sort => [1, 1, 2, 5, 7, 7, 8]