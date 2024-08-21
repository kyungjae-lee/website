[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 141. Linked List Cycle (unordered_set, Floyd's Cycle Detection Algorithm - Two Pointers)

# LC - E - 141. Linked List Cycle (unordered_set, Floyd's Cycle Detection Algorithm - Two Pointers)



## Solutions in C++

### Solution 1

This solution uses STL `unordered_set` container.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the number of elements in the given linked list
*  $S = O(n)$ as additional memory space is used for `unordered_set` container

**Solution:**

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        set<ListNode*> s;

        while (head)
        {
            if (s.find(head) != s.end())
                return true;
            
            s.insert(head);
            head = head->next;
        }
        
        return false;        
    }
};
```



### Solution 2

This solution uses the **Floyd's Cycle Detection Algorithm**; the **Two Pointers** approach. A **fast pointer** and a **slow pointer** are used to determine if there exists a cycle in the loop. 

* The **slow pointer** moves one node ahead at a time.
* The **fast pointer** moves two nodes ahead at a time.

If a loop exists in the linked list, the **fast** and **slow** pointers are bound to meet at some point.

**Complexity Analysis:**

*  $T = O(n)$ where $n$ is the length of the given string `s`
*  $S = O(1)$ as constant memory space is used

**Solution:**

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *slow = head;
        ListNode *fast = head;

        while (fast && fast->next && fast->next->next)
        {
            slow = slow->next;
            fast = fast->next->next;

            if (slow == fast)
                return true;
        }

        return false;
    }
};
```

