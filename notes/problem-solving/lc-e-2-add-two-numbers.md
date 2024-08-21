[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > LC - E - 2. Add Two Numbers

# LC - E - 2. Add Two Numbers




## Solutions in C++

### Solution 1

When adding two one-digit numbers:
- Sum without carry: `sum % 10`
- Carry: `sum / 10`

**Complexity Analysis:**

* $T=O(n)$ 

* $S=O(1)$ where $n$ is the number of elements in the longer list of the two

**Solution:**

```cpp
// Definition for singly-linked list.
// struct ListNode {
//     int val;
//     ListNode *next;
//     ListNode() : val(0), next(nullptr) {}
//     ListNode(int x) : val(x), next(nullptr) {}
//     ListNode(int x, ListNode *next) : val(x), next(next) {}
// };

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dummy(0), *curr;     // create dummy node statically to prevent
                                      // memory leak
        curr = &dummy;
        int carry = 0;
        
        while (l1 || l2 || carry)
        {
            if (l1)
            {
                carry += l1->val;
                l1 = l1->next;
            }
            
            if (l2)
            {
                carry += l2->val;
                l2 = l2->next;
            }
            
            curr->next = new ListNode(carry % 10);    // sum without carry
            curr = curr->next;
            carry /= 10;                              // carry
        }
        
        return dummy.next;    // statically created dummy node will get destroyed
                              // automatically as soon as this function terminates
    }
};
```



### Solution 2

My first solution. There are lots of redundancies in the code which could have been eliminated by using
a dummy node as is described in the *Solution 1*.

**Complexity Analysis:**

* $T=O(n)$
* $S=O(1)$ where $n$ is the number of elements in the longer list of the two

**Solution:**

```cpp
// Definition for singly-linked list.
// struct ListNode {
//     int val;
//     ListNode *next;
//     ListNode() : val(0), next(nullptr) {}
//     ListNode(int x) : val(x), next(nullptr) {}
//     ListNode(int x, ListNode *next) : val(x), next(next) {}
// };

class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry = false;
        int temp = 0;
        
        if ((temp = l1->val + l2->val + carry) >= 10) 
        {
            carry = true;
            temp %= 10;
        }

        else
        {
            carry = false;
        }

        ListNode *l = new ListNode(temp);
        ListNode *ptr = l;
        l1 = l1->next;
        l2 = l2->next;
        
        while (l1 || l2)
        {
            temp = 0;
            
            if (l1)
            {
                temp += l1->val;
                l1 = l1->next;                
            }
            if (l2)
            {
                temp += l2->val;
                l2 = l2->next;
            }
            if ((temp += carry) >= 10)
            {
                carry = true;
                temp %= 10;
            }
            else
                carry = false;
            
            ptr->next = new ListNode(temp);
            ptr = ptr->next;
            
        }
        
        if (carry)
        {
            ptr->next = new ListNode(1);
            ptr = ptr->next;
        }
        
        ptr->next = nullptr;

        return l;
    }
};
```



### Solution 3

This solution uses recursion. One drawback of this solution is that if the size of two lists differ, extra memory consumption occurs to create nodes dynamically. Just note the idea of applying recursion.

**Complexity Analysis:**

* $T=O(n)$
* $S=O(?)$ where $n$ is the number of elements in the longer list of the two

**Solution:**

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
       return addByDigit(l1, l2, 0);
    }
    
    ListNode *addByDigit(ListNode *l1, ListNode* l2, int carry)
    {
        // create dummy nodes dynamically if necessary
        if (l1 == nullptr) l1 = new ListNode(0);
        if (l2 == nullptr) l2 = new ListNode(0);
        
        // create a node that contains the sum of two digits
        ListNode *l = new ListNode((l1->val + l2->val + carry) % 10);
        carry = (l1->val + l2->val + carry) / 10;
        
        // recursive call
        if (l1->next || l2->next || carry)
            l->next = addByDigit(l1->next, l2->next, carry);
        
        // base case
        return l;
    }
};
```
