# 快速排序
快速排序一轮排序的方式是找一个元素作为基准，然后将比它小的放到一边，比它大的放到另一边，对数组中所有元素进行这么一轮操作之后，这个基准元素就已经安放到“正确”的位置了，然后递归这个基准元素的左边和右边，一直递归到某次分组只有一个元素，就完成了整个数组的排序。

python代码如下：

```
# coding: utf-8
def quick_sort(arr, left, right):
    if left < right:
        index = partition(arr, left, right)
        quick_sort(arr, left, index)
        quick_sort(arr, index + 1, right)


def partition(arr, left, right):
    key = arr[left]
    while left < right:
        while arr[right] >= key and left < right:
            right -= 1
        if left < right:
            arr[left] = arr[right]
        while arr[left] <= key and left < right:
            left += 1
        if left < right:
            arr[right] = arr[left]
    arr[left] = key
    return left


if __name__ == "__main__":
    a = [4, 2, 6, 1, 6, 9, 0]
    print(a)
    quick_sort(a, 0, len(a)-1)
    print(a)
```
这段代码是比较常见的递归实现，思路是先把数组最左边的值作为key存起来，然后从右边开始，将小于等于key的值赋给left，从左边开始，将大于等于key的值赋给right，左右向中间趋近，直到left\==right，将key回填回left\==right这个坑(这时，key的位置已经是它最终的"正确位置"，小伙伴们，想想这是为什么)， 然后返回left(也可以返回right，他俩是一样的)，外层函数再根据partiion返回的left(right)， 递归左边和右边。

