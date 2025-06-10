# 商品管理系统

一个简单高效的商品库存管理系统，用于管理商品信息、库存和调出记录。

## 项目概述

商品管理系统是一个基于Web的单页面应用程序，旨在帮助企业高效管理商品库存与信息。系统提供了直观的用户界面，支持商品的添加、修改、删除和调出操作，并能够记录和展示库存变动历史。

## 功能特点

- **用户认证**：安全的登录系统，支持多用户角色
- **商品管理**：添加、删除和修改商品信息
- **库存管理**：实时更新库存数量，支持批量调整
- **调出记录**：详细记录商品调出历史，包括时间、数量、原因和操作人
- **统计功能**：自动计算并显示商品总数、库存总值和库存总量
- **分类系统**：支持多级商品分类，便于组织和查找商品
- **响应式设计**：适配不同屏幕尺寸，提供良好的移动端体验

## 技术栈

- **前端**：HTML5, CSS3, JavaScript (原生)
- **数据存储**：浏览器本地存储 (localStorage)
- **UI设计**：自定义CSS，响应式布局

## 数据结构设计

### 1. 用户系统 (Users)
```javascript
class User {
    constructor(username, password, name, role) {
        this.username = username;  // 用户名，唯一标识符
        this.password = password;  // 密码
        this.name = name;         // 显示名称
        this.role = role;         // 用户角色
    }
}
```
- **实现方式**：使用对象数组存储
- **时间复杂度**：
  - 查找：O(n)，使用数组遍历
  - 添加：O(1)，数组末尾添加
  - 删除：O(n)，需要遍历数组
- **空间复杂度**：O(n)，n为用户数量

### 2. 商品分类树 (Category Tree)
```javascript
class CategoryNode {
    constructor(id, name) {
        this.id = id;           // 分类ID
        this.name = name;       // 分类名称
        this.children = [];     // 子分类
        this.parent = null;     // 父分类
    }
}
```
- **实现方式**：树形结构
- **时间复杂度**：
  - 查找：O(log n)，树的深度
  - 添加：O(1)，直接添加子节点
  - 删除：O(1)，移除节点连接
- **空间复杂度**：O(n)，n为节点总数

### 3. 商品管理 (Products)
```javascript
class Product {
    constructor(id, name, price, quantity, category) {
        this.id = id;               // 商品ID
        this.name = name;           // 商品名称
        this.price = price;         // 价格
        this.quantity = quantity;   // 库存数量
        this.category = category;   // 所属分类
        this.description = "";      // 描述
        this.createdAt = new Date(); // 创建时间
    }
}
```
- **实现方式**：哈希表（Object）存储
- **时间复杂度**：
  - 查找：O(1)，通过ID直接访问
  - 添加：O(1)，直接设置属性
  - 删除：O(1)，直接删除属性
- **空间复杂度**：O(n)，n为商品数量

### 4. 调出记录 (Withdrawals)
```javascript
class Withdrawal {
    constructor(productId, quantity, reason) {
        this.id = Date.now();     // 记录ID
        this.productId = productId; // 商品ID
        this.quantity = quantity;   // 调出数量
        this.reason = reason;       // 调出原因
        this.date = new Date();     // 调出时间
        this.operator = null;       // 操作人
    }
}
```
- **实现方式**：链表结构
- **时间复杂度**：
  - 添加：O(1)，头部插入
  - 查询：O(n)，需要遍历
- **空间复杂度**：O(n)，n为记录数量

### 5. 索引结构
```javascript
class ProductIndex {
    constructor() {
        this.nameIndex = new Map();    // 商品名称索引
        this.categoryIndex = new Map(); // 分类索引
        this.priceIndex = new Map();    // 价格区间索引
    }
}
```
- **实现方式**：多重索引（Map）
- **时间复杂度**：
  - 索引查找：O(1)
  - 索引更新：O(1)
- **空间复杂度**：O(n)，n为商品数量

### 数据关系图
```
Users
  └── 管理商品和库存的权限

Categories (Tree)
  ├── 子分类1
  │   └── 商品1, 商品2
  └── 子分类2
      └── 商品3, 商品4

Products (Hash Table)
  ├── ID -> Product
  └── 关联到 Categories

Withdrawals (Linked List)
  └── 关联到 Products 和 Users
```

## 核心代码结构

### 数据模型

```javascript
// 用户数据结构
const users = [
    { username: "admin", password: "admin123", name: "系统管理员" },
    { username: "manager", password: "manager123", name: "王经理" },
    { username: "staff", password: "staff123", name: "普通员工" }
];

// 商品数据结构
const products = [
    {
        id: 1234567890,
        name: "商品名称",
        price: 99.99,
        quantity: 100,
        category: "electronics|phones",
        description: "商品描述",
        createdAt: "2023-01-01T12:00:00.000Z"
    }
];

// 调出记录数据结构
const withdrawals = [
    {
        id: 1234567890,
        productId: 1234567890,
        productName: "商品名称",
        quantity: 5,
        reason: "销售",
        notes: "备注信息",
        date: "2023-01-02T12:00:00.000Z",
        operator: "系统管理员"
    }
];
```

### 核心功能实现

1. **用户认证**

```javascript
function initLoginScreen() {
    loginForm.addEventListener('submit', (e) => {
        e.preventDefault();
        const username = document.getElementById('username').value.trim();
        const password = document.getElementById('password').value;
        
        // 验证用户
        const user = users.find(u => u.username === username && u.password === password);
        
        if (user) {
            // 登录成功
            localStorage.setItem('isLoggedIn', 'true');
            localStorage.setItem('currentUser', JSON.stringify(user));
            // ...
        }
    });
}
```

2. **商品管理**

```javascript
// 添加商品
function addProduct() {
    // 获取表单数据
    const name = document.getElementById('name').value.trim();
    const price = parseFloat(document.getElementById('price').value);
    const quantity = parseInt(document.getElementById('quantity').value);
    const category = document.getElementById('category').value;
    const description = document.getElementById('description').value.trim();
    
    // 创建新商品对象
    const newProduct = {
        id: Date.now(),
        name,
        price,
        quantity,
        category,
        description,
        createdAt: new Date().toISOString()
    };
    
    // 添加到商品数组并保存
    products.push(newProduct);
    saveProducts();
}

// 删除商品
function deleteProduct(id) {
    products = products.filter(product => product.id !== id);
    saveProducts();
}
```

3. **库存管理**

```javascript
// 修改库存
function updateProductStock() {
    const newQuantity = parseInt(document.getElementById('update-quantity').value);
    const reason = document.getElementById('update-reason').value.trim() || '库存调整';
    
    const productIndex = products.findIndex(p => p.id === currentProduct.id);
    const oldQuantity = products[productIndex].quantity;
    products[productIndex].quantity = newQuantity;
    
    // 记录库存调整
    withdrawals.push({
        id: Date.now(),
        productId: currentProduct.id,
        productName: currentProduct.name,
        quantity: oldQuantity - newQuantity,
        reason: reason,
        date: new Date().toISOString(),
        operator: user ? user.name : '未知'
    });
}
```

## 安装与使用

1. 克隆或下载项目到本地
2. 使用现代浏览器打开 `product-management.html` 文件
3. 使用以下默认账号登录系统：
   - 管理员：用户名 `admin`，密码 `admin123`
   - 经理：用户名 `manager`，密码 `manager123`
   - 员工：用户名 `staff`，密码 `staff123`

## 项目截图



## 开发团队

- 王天宇
- 王玎
- 夏博文

## 未来计划

- [ ] 添加搜索和筛选功能
- [ ] 实现数据导出功能
- [ ] 添加更详细的数据分析和报表
- [ ] 支持多语言
- [ ] 添加用户权限管理

## 许可证

[MIT](LICENSE)
