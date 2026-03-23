```cpp
#include<bits/stdc++.h>
using namespace std;

class Node {
public:
    int data;
    Node* next;
    Node(int data1, Node* next1) {
        data = data1;
        next = next1;
    }
    Node(int data1) {
        data = data1;
        next = nullptr;
    }
};

// Basic Operations (already in your notes - included for completeness)
Node* convertArray2LL(int arr[], int n) {
    Node* head = new Node(arr[0]);
    Node* mover = head;
    for(int i = 1; i < n; i++) {
        Node* temp = new Node(arr[i]);
        mover->next = temp;
        mover = temp;
    }
    return head;
}

int lengthofLL(Node* head) {
    int count = 0;
    Node* temp = head;
    while(temp) {
        count++;
        temp = temp->next;
    }
    return count;
}

Node* deleteHead(Node* head) {
    if(head == NULL) return head;
    Node* temp = head;
    head = head->next;
    delete temp;
    return head;
}

// Remove kth element
Node* removek(Node* head, int k) {
    if(head == NULL) return NULL;
    if(k == 1) {
        Node* temp = head;
        head = head->next;
        delete temp;
        return head;
    }
    int count = 0;
    Node* temp = head;
    Node* prev = NULL;
    while(temp != NULL) {
        count++;
        if(count == k) {
            prev->next = prev->next->next;
            delete temp;
            break;
        }
        prev = temp;
        temp = temp->next;
    }
    return head;
}

// Remove element by value
Node* removeElement(Node* head, int val) {
    if(head == NULL) return NULL;
    
    // If head needs to be deleted
    if(head->data == val) {
        Node* temp = head;
        head = head->next;
        delete temp;
        return head;
    }
    
    Node* temp = head;
    Node* prev = NULL;
    while(temp != NULL) {
        if(temp->data == val) {
            prev->next = temp->next;
            delete temp;
            break;
        }
        prev = temp;
        temp = temp->next;
    }
    return head;
}

// Check if Linked List is Palindrome
bool isPalindromeBrute(Node* head) {
    // Brute force approach - using array
    Node* temp = head;
    vector<int> arr;
    while(temp != NULL) {
        arr.push_back(temp->data);
        temp = temp->next;
    }
    
    int front = 0;
    int end = arr.size() - 1;
    while(front < end) {
        if(arr[front] != arr[end]) return false;
        front++;
        end--;
    }
    return true;
}

Node* reverseLinkedList(Node* head) {
    Node* prev = NULL;
    Node* temp = head;
    while(temp != NULL) {
        Node* front = temp->next;
        temp->next = prev;
        prev = temp;
        temp = front;
    }
    return prev;
}

bool isPalindromeOptimal(Node* head) {
    // Optimal approach - find middle and reverse second half
    if(head == NULL || head->next == NULL) return true;
    
    Node* slow = head;
    Node* fast = head;
    
    // Find middle of linked list
    while(fast->next != NULL && fast->next->next != NULL) {
        fast = fast->next->next;
        slow = slow->next;
    }
    
    // Reverse the second half
    Node* head2 = slow->next;
    head2 = reverseLinkedList(head2);
    
    // Compare both halves
    Node* first = head;
    Node* second = head2;
    while(second != NULL) {
        if(first->data != second->data) {
            // Restore the original list before returning
            slow->next = reverseLinkedList(head2);
            return false;
        }
        first = first->next;
        second = second->next;
    }
    
    // Restore the original list
    slow->next = reverseLinkedList(head2);
    return true;
}

// Remove Nth node from end
Node* removeNthFromEnd(Node* head, int n) {
    Node* fast = head;
    Node* slow = head;
    
    // Move fast pointer n steps ahead
    for(int i = 0; i < n; i++) {
        fast = fast->next;
    }
    
    // If we need to remove head
    if(fast == NULL) {
        Node* temp = head;
        head = head->next;
        delete temp;
        return head;
    }
    
    // Move both pointers until fast reaches end
    while(fast->next != NULL) {
        fast = fast->next;
        slow = slow->next;
    }
    
    // Remove the nth node from end
    Node* delNode = slow->next;
    slow->next = slow->next->next;
    delete delNode;
    return head;
}

// Delete middle node
Node* deleteMiddle(Node* head) {
    if(head == NULL || head->next == NULL) return NULL;
    
    Node* slow = head;
    Node* fast = head;
    Node* prev = NULL;
    
    while(fast != NULL && fast->next != NULL) {
        fast = fast->next->next;
        prev = slow;
        slow = slow->next;
    }
    
    // Delete middle node
    prev->next = slow->next;
    delete slow;
    return head;
}

// Sort 0s, 1s and 2s
Node* sort012(Node* head) {
    if(head == NULL) return head;
    
    Node* zeroHead = new Node(-1);
    Node* oneHead = new Node(-1);
    Node* twoHead = new Node(-1);
    
    Node* zero = zeroHead;
    Node* one = oneHead;
    Node* two = twoHead;
    
    Node* temp = head;
    
    while(temp != NULL) {
        if(temp->data == 0) {
            zero->next = temp;
            zero = temp;
        }
        else if(temp->data == 1) {
            one->next = temp;
            one = temp;
        }
        else {
            two->next = temp;
            two = temp;
        }
        temp = temp->next;
    }
    
    // Connect the lists
    zero->next = (oneHead->next) ? oneHead->next : twoHead->next;
    one->next = twoHead->next;
    two->next = NULL;
    
    Node* newHead = zeroHead->next;
    
    // Free dummy nodes
    delete zeroHead;
    delete oneHead;
    delete twoHead;
    
    return newHead;
}

// Cycle Detection - Floyd's Cycle Finding Algorithm
bool hasCycle(Node* head) {
    if(head == NULL) return false;
    
    Node* slow = head;
    Node* fast = head;
    
    while(fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
        
        if(slow == fast) return true;
    }
    return false;
}

// Find starting point of cycle
Node* detectCycleStart(Node* head) {
    if(head == NULL || head->next == NULL) return NULL;
    
    Node* slow = head;
    Node* fast = head;
    bool hasCycle = false;
    
    // Detect cycle
    while(fast != NULL && fast->next != NULL) {
        slow = slow->next;
        fast = fast->next->next;
        
        if(slow == fast) {
            hasCycle = true;
            break;
        }
    }
    
    if(!hasCycle) return NULL;
    
    // Find starting point
    slow = head;
    while(slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }
    
    return slow;
}

// Utility function to print linked list
void printLL(Node* head) {
    Node* temp = head;
    while(temp != NULL) {
        cout << temp->data << " ";
        temp = temp->next;
    }
    cout << endl;
}

int main() {
#ifndef ONLINE_JUDGE
    freopen("input.txt", "r", stdin);
    freopen("output.txt", "w", stdout);
#endif

    // Test the implementations
    int arr[] = {1, 2, 3, 2, 1};
    int n = sizeof(arr)/sizeof(arr[0]);
    
    Node* head = convertArray2LL(arr, n);
    cout << "Original List: ";
    printLL(head);
    
    cout << "Is Palindrome (Brute): " << isPalindromeBrute(head) << endl;
    cout << "Is Palindrome (Optimal): " << isPalindromeOptimal(head) << endl;
    
    head = removeNthFromEnd(head, 2);
    cout << "After removing 2nd from end: ";
    printLL(head);
    
    head = deleteMiddle(head);
    cout << "After deleting middle: ";
    printLL(head);
    
    // Test sort 0,1,2
    int arr2[] = {2, 1, 0, 2, 1, 0};
    Node* head2 = convertArray2LL(arr2, 6);
    cout << "Before sorting 0,1,2: ";
    printLL(head2);
    head2 = sort012(head2);
    cout << "After sorting 0,1,2: ";
    printLL(head2);
    
    return 0;
}
```

