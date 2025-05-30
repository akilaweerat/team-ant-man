# SmartCart Entity Definitions

This document defines the core entities and their relationships in the SmartCart system, mapping to the features defined in user_features.md.

## Core Entities

### User
```typescript
interface User {
  id: string;
  email: string;
  name: string;
  role: 'customer' | 'admin';
  phone?: string;
  addresses: Address[];
  preferences: UserPreferences;
  createdAt: Date;
  updatedAt: Date;
}

interface UserPreferences {
  theme: 'light' | 'dark' | 'system';
  language: string;
  currency: string;
  notifications: NotificationPreferences;
}

interface NotificationPreferences {
  email: boolean;
  sms: boolean;
  push: boolean;
  stockAlerts: boolean;
  priceAlerts: boolean;
}
```

### Product
```typescript
interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  discountPrice?: number;
  images: string[];
  category: Category;
  stock: number;
  rating: number;
  reviews: Review[];
  specifications: Record<string, string>;
  variants?: ProductVariant[];
  status: 'active' | 'inactive';
  createdAt: Date;
  updatedAt: Date;
}

interface ProductVariant {
  id: string;
  productId: string;
  name: string;
  price: number;
  stock: number;
  attributes: Record<string, string>; // e.g., { color: 'red', size: 'M' }
}
```

### Category
```typescript
interface Category {
  id: string;
  name: string;
  description?: string;
  parentId?: string;
  image?: string;
  order: number;
  isActive: boolean;
}
```

### Cart
```typescript
interface Cart {
  id: string;
  userId: string;
  items: CartItem[];
  createdAt: Date;
  updatedAt: Date;
}

interface CartItem {
  id: string;
  cartId: string;
  productId: string;
  variantId?: string;
  quantity: number;
  addedAt: Date;
}
```

### Order
```typescript
interface Order {
  id: string;
  userId: string;
  items: OrderItem[];
  status: OrderStatus;
  shippingAddress: Address;
  billingAddress: Address;
  payment: Payment;
  shipping: Shipping;
  subtotal: number;
  tax: number;
  shippingCost: number;
  total: number;
  createdAt: Date;
  updatedAt: Date;
}

interface OrderItem {
  id: string;
  orderId: string;
  productId: string;
  variantId?: string;
  quantity: number;
  price: number;
  name: string;
}

enum OrderStatus {
  PENDING = 'pending',
  PROCESSING = 'processing',
  SHIPPED = 'shipped',
  DELIVERED = 'delivered',
  CANCELLED = 'cancelled'
}
```

### Address
```typescript
interface Address {
  id: string;
  userId: string;
  type: 'shipping' | 'billing';
  street: string;
  city: string;
  state: string;
  country: string;
  postalCode: string;
  isDefault: boolean;
}
```

### Review
```typescript
interface Review {
  id: string;
  userId: string;
  productId: string;
  rating: number;
  comment: string;
  images?: string[];
  createdAt: Date;
  updatedAt: Date;
}
```

### Wishlist
```typescript
interface Wishlist {
  id: string;
  userId: string;
  name: string;
  items: WishlistItem[];
  isPublic: boolean;
  createdAt: Date;
  updatedAt: Date;
}

interface WishlistItem {
  id: string;
  wishlistId: string;
  productId: string;
  addedAt: Date;
}
```

### Payment
```typescript
interface Payment {
  id: string;
  orderId: string;
  amount: number;
  status: PaymentStatus;
  method: PaymentMethod;
  transactionId?: string;
  createdAt: Date;
  updatedAt: Date;
}

enum PaymentStatus {
  PENDING = 'pending',
  COMPLETED = 'completed',
  FAILED = 'failed',
  REFUNDED = 'refunded'
}

enum PaymentMethod {
  CREDIT_CARD = 'credit_card',
  DEBIT_CARD = 'debit_card',
  PAYPAL = 'paypal',
  BANK_TRANSFER = 'bank_transfer'
}
```

### Shipping
```typescript
interface Shipping {
  id: string;
  orderId: string;
  method: ShippingMethod;
  status: ShippingStatus;
  trackingNumber?: string;
  estimatedDelivery?: Date;
  actualDelivery?: Date;
  cost: number;
}

enum ShippingMethod {
  STANDARD = 'standard',
  EXPRESS = 'express',
  NEXT_DAY = 'next_day'
}

enum ShippingStatus {
  PENDING = 'pending',
  PROCESSING = 'processing',
  SHIPPED = 'shipped',
  DELIVERED = 'delivered',
  FAILED = 'failed'
}
```

## Entity Relationships

1. **User Relationships**
   - One-to-Many with Address
   - One-to-Many with Order
   - One-to-Many with Review
   - One-to-Many with Wishlist
   - One-to-One with Cart

2. **Product Relationships**
   - Many-to-One with Category
   - One-to-Many with ProductVariant
   - One-to-Many with Review
   - Many-to-Many with Wishlist (through WishlistItem)

3. **Order Relationships**
   - Many-to-One with User
   - One-to-Many with OrderItem
   - One-to-One with Payment
   - One-to-One with Shipping

4. **Cart Relationships**
   - One-to-One with User
   - One-to-Many with CartItem

5. **Category Relationships**
   - One-to-Many with Product
   - Self-referential (parent-child relationship)

## Notes

- All entities include `id`, `createdAt`, and `updatedAt` fields for tracking and auditing
- Timestamps are stored in UTC
- Monetary values are stored in the smallest currency unit (e.g., cents for USD)
- All IDs are UUIDs for security and scalability
- Soft deletion is implemented where appropriate (not shown in interfaces for brevity) 