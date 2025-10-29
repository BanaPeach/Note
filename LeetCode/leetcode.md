# LeetCode 刷题

作者：Yan

## 二、两数相加

> 链表操作 + 模拟加法

1. 解题代码

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode head = new ListNode(0);

        // 头节点
        ListNode cur = head;
        // 指针
        int carry = 0;

        while(l1 != null || l2 != null) {
            // 处理链表长度不一的情况
            int num1 = (l1 == null) ? 0 : l1.val;
            int num2 = (l2 == null) ? 0 : l2.val;

            // 当前位之和
            int sum = num1 + num2 + carry;

            // 进位
            carry =  sum / 10;

            cur.next = new ListNode(sum % 10);
            cur = cur.next;

            // 处理链表长度不一的情况
            if (l1 != null) l1 = l1.next;
            if (l2 != null) l2 = l2.next;
        }
        // 如果最后还有进位补上
        if (carry > 0) {
            cur.next = new ListNode(carry);
        }

        return head.next;

    }
}
```