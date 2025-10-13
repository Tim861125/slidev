---
theme: seriph
background: https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=1920
title: Zod - TypeScript Schema Validation
info: |
  ## Zod Technical Report
  TypeScript Data Validation Best Practices
class: text-center
highlighter: shiki
transition: slide-left
mdc: true
---
# Zod

TypeScript-first Schema Validation Library

---

# What is Zod?

A TypeScript-first schema declaration and validation library

<br>

## Core Features

- **Type Safety First** - Fully leverages TypeScript's type system
- **Zero Dependencies** - Lightweight design
- **Great DX** - Intuitive and clean API
- **Runtime Validation** - Complements TypeScript's compile-time checking

---

# Why Do We Need Zod?

<div grid="~ cols-2 gap-4">

<div v-click>

## The Problem

```ts
// TypeScript only checks at compile time
interface User {
  name: string;
  age: number;
}

// Runtime type is not guaranteed
const data = await fetch('/api/user')
  .then(r => r.json()) as User;

// What if API response changes?
// What if fields are missing?
// What if types don't match?
```

</div>

<div v-click>

## The Solution

```ts
// Zod provides runtime validation
import { z } from 'zod';

const UserSchema = z.object({
  name: z.string(),
  age: z.number()
});

// Validate at runtime
const user = UserSchema.parse(data);
// user is guaranteed to be correct
```

</div>

</div>

---

# Interactive Demo

<ZodDemo />

---

# Basic Type Validation

<div grid="~ cols-2 gap-4">

<div>

```ts
// String
z.string()
z.string().min(3).max(100)
z.string().email()
z.string().url()
z.string().uuid()
z.string().regex(/^\d{4}$/)

// Number
z.number()
z.number().int()
z.number().positive()
z.number().min(0).max(100)

// Boolean
z.boolean()
```

</div>

<div>

```ts
// Date
z.date()
z.date().min(new Date("2020-01-01"))

// Enum
z.enum(["admin", "user", "guest"])

// Literal
z.literal("hello")
z.literal(42)

// Optional & Nullable
z.string().optional()  // string | undefined
z.string().nullable()  // string | null
z.string().nullish()   // string | null | undefined

// Default values
z.string().default("guest")
z.number().default(0)
```

</div>

</div>

---

# Object Validation

```ts
// Basic object
const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
});

// Nested object
const ProfileSchema = z.object({
  user: UserSchema,
  settings: z.object({
    theme: z.enum(['light', 'dark']),
    notifications: z.boolean(),
  }),
});

// Partial fields & picking
UserSchema.partial()              // All fields optional
UserSchema.pick({ name: true })   // Only keep name
UserSchema.omit({ id: true })     // Exclude id
```

---

# Array Validation

```ts
// Basic array
z.array(z.string())           // string[]
z.string().array()            // Same as above

// Array length constraints
z.array(z.number()).min(1).max(10)
z.array(z.string()).nonempty()   // At least one element

// Tuple
z.tuple([z.string(), z.number(), z.boolean()])
// [string, number, boolean]
```

---

# Complex Validation

```ts
// Union types
const StringOrNumber = z.union([z.string(), z.number()])
const Result = z.string().or(z.number())  // Shorthand

// Discriminated Union
const Response = z.discriminatedUnion('status', [
  z.object({ status: z.literal('success'), data: z.any() }),
  z.object({ status: z.literal('error'), error: z.string() }),
])

// Intersection
const BaseUser = z.object({ id: z.number() })
const NamedEntity = z.object({ name: z.string() })
const User = BaseUser.and(NamedEntity)

// Record - key-value pairs
z.record(z.string(), z.number())  // { [key: string]: number }
```

---

# Conditional Validation

```ts
const ConditionalSchema = z.object({
  type: z.enum(['personal', 'business']),
  taxId: z.string().optional(),
}).refine((data) => {
  // Business accounts require taxId
  if (data.type === 'business') {
    return data.taxId !== undefined;
  }
  return true;
}, {
  message: "Business accounts require taxId",
  path: ["taxId"],
});
```

---

# Validation Methods

<div grid="~ cols-2 gap-4">

<div>

## parse vs safeParse

```ts
const schema = z.string();

// parse - throws error on failure
try {
  const result = schema.parse("hello");
  // result: string
} catch (error) {
  // ZodError
}

// safeParse - returns Result object
const result = schema.safeParse("hello");

if (result.success) {
  console.log(result.data);  // string
} else {
  console.log(result.error); // ZodError
}
```

</div>

<div>

## Async Validation

```ts
const asyncSchema = z.string().refine(
  async (val) => {
    // Simulate API check
    const exists = await checkUserExists(val);
    return !exists;
  },
  { message: "Username already taken" }
);

// Use parseAsync
const username = await asyncSchema
  .parseAsync("john");

// Or use safeParseAsync
const result = await asyncSchema
  .safeParseAsync("john");
```

</div>

</div>

---

# Error Handling

```ts
const UserSchema = z.object({
  email: z.string().email(),
  age: z.number().min(18),
});

const result = UserSchema.safeParse({
  email: "invalid-email",
  age: 15,
});

if (!result.success) {
  const error = result.error;

  // Complete error information
  console.log(error.issues);
}
```

---

# Error Details

```ts
// Error issues array
[
  {
    code: 'invalid_string',
    validation: 'email',
    path: ['email'],
    message: 'Invalid email'
  },
  {
    code: 'too_small',
    minimum: 18,
    path: ['age'],
    message: 'Number must be greater than or equal to 18'
  }
]

// Formatted output
{
  email: { _errors: ['Invalid email'] },
  age: { _errors: ['Number must be greater than or equal to 18'] }
}
```

---

layout: center
class: text-center
------------------

# Zod v4 Major Improvements

Key updates from v3 to v4

---

# Performance Improvements

<div grid="~ cols-2 gap-4">

<div>

## Speed Boost

- **Faster validation**: Rewritten core engine
- **Memory optimization**: ~30% reduction
- **Lazy initialization**: Schemas built on first use

```ts
// v4 Performance benchmarks
// Simple objects: ~2-3x faster
// Complex nested: ~1.5-2x faster
```

</div>

<div>

## Bundle Size

- Core code size reduced by **~15%**
- Better tree-shaking support
- Separated optional features

</div>

</div>

---

# Better Type Inference

<div grid="~ cols-2 gap-4">

<div>

## v3 Limitations

```ts
// v3 - Deep nesting could fail
const deep = z.object({
  a: z.object({
    b: z.object({
      c: z.string()
    })
  })
});
type Deep = z.infer<typeof deep>;
// Type might be too complex
```

</div>

<div>

## v4 Improvements

```ts
// v4 - Better deep type inference
// Supports more complex recursive structures
// Fewer "Type instantiation is
// excessively deep" errors
```

</div>

</div>

---

# New API Features

## 1. Pipe API

```ts
// v4 new - More elegant data transformation chains
const schema = z.string()
  .pipe(z.coerce.number())
  .pipe(z.number().positive());

schema.parse("123");  // 123 (number)
```

## 2. Improved Error Customization

```ts
// v4 - More powerful error message customization
const schema = z.string({
  required_error: "Name is required",
  invalid_type_error: "Name must be a string",
});

// Function-based custom messages
z.string().min(3, (val) => `${val} is too short, need at least 3 characters`);
```

---

# Coercion Enhancement

```ts
// v4 - Built-in type coercion
z.coerce.string()    // Force to string
z.coerce.number()    // Force to number
z.coerce.boolean()   // Force to boolean
z.coerce.date()      // Force to date

// Examples
z.coerce.number().parse("123");   // 123
z.coerce.boolean().parse("true"); // true
```

---

# Brand Types & Readonly

<div grid="~ cols-2 gap-4">

<div>

## Brand Types

```ts
// v4 - Improved brand type support
const UserId = z.string().brand<"UserId">();
const Email = z.string().email().brand<"Email">();

type UserId = z.infer<typeof UserId>;
type Email = z.infer<typeof Email>;

// Type-level distinction
function getUser(id: UserId) { /* ... */ }

const email: Email =
  Email.parse("test@example.com");
// getUser(email); // Type error
```

</div>

<div>

## Readonly Support

```ts
// v4 - Built-in readonly support
const schema = z.object({
  name: z.string()
}).readonly();

type T = z.infer<typeof schema>;
// { readonly name: string }
```

</div>

</div>

---

# Catch & SuperRefine

<div grid="~ cols-2 gap-4">

<div>

## Catch with Defaults

```ts
// v4 - More flexible error handling
const schema = z.string().catch("default");

schema.parse(123);  // "default"

// Dynamic defaults
z.string().catch((ctx) => {
  console.log(ctx.error);
  return "fallback";
});
```

</div>

<div>

## SuperRefine Enhancement

```ts
// v4 - More powerful custom validation
z.object({
  password: z.string(),
  confirm: z.string()
}).superRefine((data, ctx) => {
  if (data.password !== data.confirm) {
    ctx.addIssue({
      code: z.ZodIssueCode.custom,
      message: "Passwords don't match",
      path: ["confirm"]
    });
  }
});
```

</div>

</div>

---

layout: center
--------------

# Real-World Use Cases

Integrating Zod into actual projects

---

# Case 1: API Route Validation (Express)

```ts
import express from 'express';
import { z } from 'zod';

const CreateUserSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(2),
  role: z.enum(['admin', 'user']).default('user'),
});

// Auto-infer type
type CreateUserInput = z.infer<typeof CreateUserSchema>;

const app = express();

app.post('/users', async (req, res) => {
  const result = CreateUserSchema.safeParse(req.body);

  if (!result.success) {
    return res.status(400).json({
      error: 'Validation failed',
      issues: result.error.format(),
    });
  }
```

---

# Case 1: API Route (continued)

```ts
  // result.data is type-safe
  const user = await createUser(result.data);

  res.json(user);
});
```

---

# Case 2: Form Validation (React)

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const formSchema = z.object({
  username: z.string().min(3, 'Min 3 characters'),
  email: z.string().email('Invalid email'),
  age: z.coerce.number().min(18, 'Must be 18+'),
});

type FormData = z.infer<typeof formSchema>;

function RegistrationForm() {
  const { register, handleSubmit, formState: { errors } }
    = useForm<FormData>({
      resolver: zodResolver(formSchema),
    });

  const onSubmit = (data: FormData) => {
    console.log(data); // Type-safe data
  };
```

---

# Case 2: Form Validation (continued)

```tsx
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('username')} />
      {errors.username && <span>{errors.username.message}</span>}

      <input type="email" {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}

      <input type="number" {...register('age')} />
      {errors.age && <span>{errors.age.message}</span>}

      <button type="submit">Register</button>
    </form>
  );
}
```

---

# Case 3: Environment Variables

```ts
// env.ts
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(1),
  PORT: z.coerce.number().default(3000),
  REDIS_HOST: z.string().default('localhost'),
  REDIS_PORT: z.coerce.number().default(6379),
});

// Validate and export
const env = envSchema.parse(process.env);
export default env;

// Type inference
type Env = z.infer<typeof envSchema>;
// {
//   NODE_ENV: 'development' | 'production' | 'test';
//   DATABASE_URL: string;
//   API_KEY: string;
//   ...
// }
```

---

# Case 3: Using Environment Variables

```ts
// Usage with full type safety
import env from './env';

console.log(env.DATABASE_URL); // Type-safe
console.log(env.UNKNOWN);      // Compile error
```

---

# Case 4: API Response Validation

```ts
// schemas/user.ts
import { z } from 'zod';

export const UserSchema = z.object({
  id: z.number(),
  email: z.string().email(),
  name: z.string(),
  avatar: z.string().url().optional(),
  createdAt: z.string().transform((str) => new Date(str)),
});

export const UsersResponseSchema = z.object({
  users: z.array(UserSchema),
  total: z.number(),
  page: z.number(),
});

// Auto-infer types
export type User = z.infer<typeof UserSchema>;
export type UsersResponse = z.infer<typeof UsersResponseSchema>;

// api/users.ts
async function fetchUsers(page: number): Promise<UsersResponse> {
  const response = await fetch(`/api/users?page=${page}`);
  const json = await response.json();

  // Validate API response
  const data = UsersResponseSchema.parse(json);
```

---

# Case 4: API Response (continued)

```ts
  // data.users[0].createdAt is already a Date object
  return data;
}

// Usage
const result = await fetchUsers(1);
result.users.forEach(user => {
  console.log(user.name);                    // string
  console.log(user.createdAt);               // Date
  console.log(user.createdAt.getFullYear()); // Method available
});
```

---

# Case 5: Complex Business Logic

```ts
import { z } from 'zod';

// Order Schema - Complex business rules
const OrderSchema = z.object({
  items: z.array(
    z.object({
      productId: z.string().uuid(),
      quantity: z.number().int().positive(),
      price: z.number().positive(),
    })
  ).min(1, 'Order must have at least one item'),

  shippingAddress: z.object({
    street: z.string().min(1),
    city: z.string().min(1),
    postalCode: z.string().regex(/^\d{5}$/, 'Invalid postal code'),
    country: z.string().length(2, 'Use ISO country code'),
  }),
```

---

# Case 5: Complex Business Logic (2)

```ts
  discount: z.number().min(0).max(100).optional(),

  paymentMethod: z.enum(['credit_card', 'paypal', 'bank_transfer']),

  // Credit card info - conditionally required
  creditCard: z.object({
    number: z.string().regex(/^\d{16}$/),
    cvv: z.string().regex(/^\d{3,4}$/),
    expiryMonth: z.number().min(1).max(12),
    expiryYear: z.number().min(new Date().getFullYear()),
  }).optional(),

}).refine((data) => {
  // If using credit card, must provide card info
  if (data.paymentMethod === 'credit_card') {
    return data.creditCard !== undefined;
  }
  return true;
}, {
```

---

# Case 5: Complex Business Logic (3)

```ts
  message: 'Credit card payment requires card info',
  path: ['creditCard'],
}).refine((data) => {
  // Total amount validation
  const total = data.items.reduce((sum, item) =>
    sum + (item.price * item.quantity), 0
  );
  return total > 0;
}, {
  message: 'Order total must be greater than 0',
});

type Order = z.infer<typeof OrderSchema>;
```

---

# Best Practices

<div grid="~ cols-2 gap-4">

<div>

## 1. Validate at Boundaries

- API endpoints
- External data sources
- User inputs

## 2. Centralize Schemas

```ts
// schemas/index.ts
export const schemas = {
  user: UserSchema,
  post: PostSchema,
  comment: CommentSchema,
};
```

</div>

<div>

## 3. Use z.infer to Avoid Duplication

```ts
// Good
const User = z.object({...});
type User = z.infer<typeof User>;

// Bad - Duplicate definitions
interface User {...}
const UserSchema = z.object({...});
```

</div>

</div>

---

# Best Practices (continued)

<div grid="~ cols-2 gap-4">

<div>

## 4. Use Transform for Data Processing

```ts
z.string()
  .transform(s => s.toLowerCase())
  .pipe(z.string().email());
```

## 5. Error Message i18n

```ts
import { z } from 'zod';
import { zodI18nMap } from 'zod-i18n-map';
import translation from
  'zod-i18n-map/locales/zh-TW/zod.json';

z.setErrorMap(zodI18nMap);
```

</div>

<div>

## 6. Build Reusable Schemas

```ts
const TimestampSchema = z.object({
  createdAt: z.date(),
  updatedAt: z.date(),
});

const UserSchema = BaseUserSchema
  .merge(TimestampSchema);
```

</div>

</div>

---

# Common Challenges

<div grid="~ cols-2 gap-4">

<div>

## Challenge 1: Performance

**Problem:** Large object/array validation is slow

**Solution:**

```ts
// Use lazy for recursive validation
const CategorySchema: z.ZodType<Category> =
  z.lazy(() => z.object({
    name: z.string(),
    children: z.array(CategorySchema),
  }));

// Partial validation
const PartialUpdate = UserSchema
  .partial()
  .pick({ name: true, email: true });
```

</div>

<div>

## Challenge 2: Circular References

**Solution:**

```ts
type Node = {
  value: string;
  children: Node[];
};

const NodeSchema: z.ZodType<Node> =
  z.lazy(() => z.object({
    value: z.string(),
    children: z.array(NodeSchema),
  }));
```

</div>

</div>

---

# More Challenges & Solutions

<div grid="~ cols-2 gap-4">

<div>

## Challenge 3: Integration

**Strategy:**

1. Gradual adoption
2. Start with new features
3. Critical paths first

```ts
// Wrapper function for easy migration
function validate<T>(
  schema: z.ZodType<T>,
  data: unknown
): T {
  return schema.parse(data);
}

// Usage
const user = validate(UserSchema, apiData);
```

</div>

<div>

## Challenge 4: Bundle Size

**Optimization:**

```ts
// Import only what you need
import { z } from 'zod';

// Use tree-shaking
// Ensure bundler is configured correctly
```

</div>

</div>

---

# Comparison with Other Libraries

| Feature                | Zod          | Yup         | Joi         | io-ts   |
| ---------------------- | ------------ | ----------- | ----------- | ------- |
| TypeScript First       | ✅           | ⚠️        | ❌          | ✅      |
| Zero Dependencies      | ✅           | ❌          | ❌          | ❌      |
| Bundle Size (min+gzip) | ~13kb        | ~18kb       | ~45kb       | ~8kb    |
| Type Inference         | ✅ Excellent | ⚠️ OK     | ❌          | ✅      |
| API Ease of Use        | ✅           | ✅          | ✅          | ⚠️    |
| Async Validation       | ✅           | ✅          | ✅          | ❌      |
| Transformations        | ✅           | ✅          | ✅          | ⚠️    |
| Error Messages         | ✅ Detailed  | ✅          | ✅          | ⚠️    |
| Performance            | ✅ Fast      | ⚠️ Medium | ⚠️ Medium | ✅ Fast |

---

# When to Choose What?

## Recommendations

- **New project + TypeScript**: Zod ⭐
- **Already using Yup**: Consider migration
- **Node.js backend**: Zod or Joi
- **Extreme lightweight**: io-ts

---

# Advanced Techniques

<div grid="~ cols-2 gap-4">

<div>

## 1. Schema Composition Pattern

```ts
// Base schemas
const Timestamps = z.object({
  createdAt: z.date(),
  updatedAt: z.date(),
});

const SoftDelete = z.object({
  deletedAt: z.date().nullable(),
});

// Compose them
const Entity = z.object({
  id: z.string().uuid(),
}).merge(Timestamps).merge(SoftDelete);
```

</div>

<div>

## 2. Preprocessor Pattern

```ts
const preprocessor = z.preprocess(
  (val) => {
    if (typeof val === 'string') {
      return JSON.parse(val);
    }
    return val;
  },
  z.object({ name: z.string() })
);
```

</div>

</div>

---

# Advanced Techniques (continued)

<div grid="~ cols-2 gap-4">

<div>

## 3. Conditional Schema

```ts
const ConditionalSchema =
  z.discriminatedUnion('type', [
    z.object({
      type: z.literal('email'),
      email: z.string().email(),
    }),
    z.object({
      type: z.literal('phone'),
      phone: z.string().regex(/^\d{10}$/),
    }),
  ]);
```

</div>

<div>

## 4. Custom Validators

```ts
const customString =
  z.custom<`custom-${string}`>(
    (val) => {
      return typeof val === 'string' &&
             val.startsWith('custom-');
    }
  );

type Custom = z.infer<typeof customString>;
// `custom-${string}`
```

</div>

</div>

---

layout: center
class: text-center
------------------

# Summary

---

# Key Takeaways

<div grid="~ cols-2 gap-6">

<div>

## Zod's Advantages

1. **Type Safety**

   - TypeScript-first design
   - Excellent type inference
   - Compile + runtime protection
2. **Developer Experience**

   - Intuitive API
   - Detailed error messages
   - Zero dependencies, lightweight
3. **Feature Complete**

   - Rich validators
   - Data transformation
   - Async support

</div>

<div>

## Use Cases

✅ **Highly Recommended**

- TypeScript projects
- API development (frontend/backend)
- Form validation
- Config validation
- External data validation

⚠️ **Evaluate Carefully**

- Minimal projects (might be over-engineering)
- Pure JavaScript projects
- Extreme focus on bundle size

</div>

</div>

---

# v4 Improvements Summary

<div grid="~ cols-2 gap-6">

<div>

## Performance

- 2-3x faster for simple objects
- 1.5-2x faster for complex structures
- 30% less memory usage
- 15% smaller bundle size

</div>

<div>

## New Features

- Pipe API for transformations
- Enhanced coercion
- Better brand types
- Improved error customization
- Stronger type inference

</div>

</div>

---

# Learning Resources

<div class="pt-8">

## Official Resources

- [Official Documentation](https://zod.dev)
- [GitHub Repository](https://github.com/colinhacks/zod)
- [Zod Playground](https://stackblitz.com/edit/zod-playground)

## Community

- [Community Discord](https://discord.gg/zod)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/zod)

</div>

---

layout: end
class: text-center
------------------

# Thank You!

## Q&A

<div class="pt-12 text-sm opacity-75">

📚 References

[Zod Documentation](https://zod.dev) ·
[GitHub](https://github.com/colinhacks/zod) ·
[TypeScript Handbook](https://www.typescriptlang.org/docs/)

</div>
