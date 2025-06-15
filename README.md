Вариант 19
Задачи 1 и 2

#include <iostream>
#include <stack>
#include <vector>

template <class T>
class Tree {
public:
    struct Node {
        Node* Left;
        Node* Right;
        T Value;

        Node(const T& value) : Left(nullptr), Right(nullptr), Value(value) {}
    };

    Tree() : root(nullptr) {}

    ~Tree() {
        Clear(root);
    }

    void Add(const T& value) {
        root = Add(root, value);
    }

    Node* Find(const T& value) {
        return Find(root, value);
    }

    void Remove(Node* node) {
        root = Remove(root, node);
    }

    // Обратный обход (Postorder)
    std::vector<T> PostorderTraversal() {
        std::vector<T> result;
        PostorderTraversal(root, result);
        return result;
    }

private:
    Node* root;

    // Рекурсивная версия Postorder
    void PostorderTraversal(Node* node, std::vector<T>& result) {
        if (!node) return;

        PostorderTraversal(node->Left, result);  // 1. Левое поддерево
        PostorderTraversal(node->Right, result); // 2. Правое поддерево
        result.push_back(node->Value);          // 3. Корень
    }

    // Итеративная версия Postorder (на случай, если рекурсия нежелательна)
    void PostorderTraversalIterative(Node* node, std::vector<T>& result) {
        if (!node) return;

        std::stack<Node*> stack;
        Node* lastVisited = nullptr;
        Node* current = node;

        while (current || !stack.empty()) {
            if (current) {
                stack.push(current);
                current = current->Left;
            }
            else {
                Node* topNode = stack.top();
                if (topNode->Right && topNode->Right != lastVisited) {
                    current = topNode->Right;
                }
                else {
                    result.push_back(topNode->Value);
                    lastVisited = topNode;
                    stack.pop();
                }
            }
        }
    }

    // Остальные методы (Add, Find, Remove, FindMin, Clear) остаются без изменений
    // ...
};

int main() {
    Tree<int> bst;

    bst.Add(9);
    bst.Add(55);
    bst.Add(3);
    bst.Add(2);
    bst.Add(3);
    bst.Add(11);
    bst.Add(77);

    std::vector<int> postorder = bst.PostorderTraversal();

    std::cout << "Postorder traversal: ";
    for (int val : postorder) {
        std::cout << val << " ";
    }
    std::cout << std::endl;

    return 0;
}

Задача 3

#include <iostream>
#include <vector>
#include <queue>
#include <sstream>
#include <string>

template <class T>
class BinarySearchTree {
public:
    struct Node {
        Node* Left;
        Node* Right;
        T Value;
        
        Node(const T& value) : Left(nullptr), Right(nullptr), Value(value) {}
    };

    BinarySearchTree() : root(nullptr) {}
    
    ~BinarySearchTree() {
        Clear(root);
    }

    // Метод сериализации дерева в строку
    std::string serialize() {
        if (!root) return "";
        
        std::vector<std::string> tokens;
        std::queue<Node*> q;
        q.push(root);
        
        while (!q.empty()) {
            Node* current = q.front();
            q.pop();
            
            if (current) {
                tokens.push_back(std::to_string(current->Value));
                q.push(current->Left);
                q.push(current->Right);
            } else {
                tokens.push_back("N");
            }
        }
        
        // Удаляем лишние "N" в конце
        while (!tokens.empty() && tokens.back() == "N") {
            tokens.pop_back();
        }
        
        std::string result;
        for (size_t i = 0; i < tokens.size(); ++i) {
            if (i != 0) result += ",";
            result += tokens[i];
        }
        
        return result;
    }

    // Метод десериализации строки в дерево
    void deserialize(const std::string& data) {
        Clear(root);
        root = nullptr;
        
        if (data.empty()) return;
        
        std::vector<std::string> tokens;
        std::string token;
        std::istringstream iss(data);
        
        while (std::getline(iss, token, ',')) {
            tokens.push_back(token);
        }
        
        if (tokens.empty()) return;
        
        std::queue<Node*> q;
        size_t index = 0;
        
        root = new Node(std::stoi(tokens[index++]));
        q.push(root);
        
        while (!q.empty() && index < tokens.size()) {
            Node* current = q.front();
            q.pop();
            
            // Обработка левого потомка
            if (index < tokens.size() && tokens[index] != "N") {
                current->Left = new Node(std::stoi(tokens[index]));
                q.push(current->Left);
            }
            index++;
            
            // Обработка правого потомка
            if (index < tokens.size() && tokens[index] != "N") {
                current->Right = new Node(std::stoi(tokens[index]));
                q.push(current->Right);
            }
            index++;
        }
    }

private:
    Node* root;
    void Clear(Node* node) {
        if (!node) return;
        
        Clear(node->Left);
        Clear(node->Right);
        delete node;
    }
};
