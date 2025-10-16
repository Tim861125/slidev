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



<br>

- A TypeScript-first schema declaration and validation library

---

# Why Do We Need Zod?

<div grid="~ cols-2 gap-4">

<div>

## The Problem

```ts {1-5|7-13}
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

# safeParse() parse()

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
layout: center
class: text-center
---

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

</div>

</div>

---
layout: two-cols
---

# Performance

Comprehensive performance optimizations

::right::

<v-clicks>

### Runtime Speed

- **String parsing** 14x faster
- **Array parsing** 7x faster  
- **Object parsing** 6.5x faster

<br>

### Compilation Performance

- Chaining `.extend()` & `.omit()`
  - v3: 4000ms
  - v4: 400ms (10x faster)

</v-clicks>

---

# 📦 Bundle Size Optimization

Three options for different needs

| Version | Gzip Size | vs v3 |
|---------|-----------|-------|
| Zod v3 | 12.47 kb | - |
| Zod v4 (regular) | 5.36 kb | **-57%** |
| Zod v4 (mini) | 1.88 kb | **-85%** |

<v-click>

### Zod Mini - Functional API
```ts
// Regular Zod
z.string().email().optional()

// Zod Mini (better tree-shaking)
optional(email(string()))
```

</v-click>

---
layout: two-cols
---

# New Schema Types

More practical validation types

::right::

<v-clicks>

### Basic Types
```ts
// File validation
z.file()

// Template literals
z.templateLiteral`user_${z.string()}`

// Environment boolean
z.stringbool()
```

### Number Formats
```ts
z.int8()    // -128 to 127
z.uint32()  // 0 to 4,294,967,295
z.float32() // 32-bit float
z.float64() // 64-bit float
```

### BigInt Formats
```ts
z.int64()   // 64-bit integer
z.uint64()  // unsigned 64-bit
```

</v-clicks>

---

# 🎯 Recursive Object Support

No more type casting required!

<v-clicks>
```ts {all|1-4|6-10|all}
// ✅ v4: Native support
const category = z.object({
  name: z.string(),
  subcategories: z.lazy(() => z.array(category))
});

// Mutually recursive works too
const person: z.ZodType<Person> = z.object({
  name: z.string(),
  friends: z.lazy(() => z.array(person))
});
```

<div v-click class="mt-4 p-4 bg-green-500/10 rounded">
Full <code>ZodObject</code> instance with all methods available
</div>

</v-clicks>

---

# 🏷️ Metadata System

Strongly-typed metadata support

<v-clicks>
```ts {all|1-4|6-10|12-15|all}
// Global registry
z.object({
  email: z.email().meta({ description: "User email address" })
});

// Custom registry
const myRegistry = z.registry<{
  label: string;
  category: "public" | "private";
}>();

// Automatic JSON Schema conversion
const jsonSchema = z.toJSONSchema(mySchema);
// metadata automatically included in output
```

</v-clicks>

---
layout: two-cols
---

# 🔧 API Improvements

More concise and consistent

::right::

<v-clicks>

### String formats promoted to top-level
```ts
// ❌ v3 (deprecated)
z.string().email()

// ✅ v4 (recommended)
z.email()

// Custom regex
z.email({ 
  regex: z.emailRegex.rfc5322 
})
```

### Literal supports multiple values
```ts
z.literal("a", "b", "c")
// equivalent to
z.union([
  z.literal("a"),
  z.literal("b"),
  z.literal("c")
])
```

</v-clicks>

---

# 🎨 Enhanced Error Handling

More user-friendly error messages

<v-clicks>

### Unified error parameter
```ts
// ❌ v3: Multiple parameters
z.string({ 
  required_error: "Required",
  invalid_type_error: "Invalid type" 
})

// ✅ v4: Unified error
z.string({ 
  error: "Please enter a valid string" 
})
```

### Pretty-print errors
```ts
const error = schema.safeParse(data).error;
console.log(z.prettifyError(error));
```

### Internationalization
```ts
z.locales.setLocale("zh-TW");
```

</v-clicks>

---

# 🔄 Refinements Built-in

More flexible method chaining

<v-clicks>
```ts {all|2-5|7-11|all}
// ❌ v3: Cannot interleave
z.string()
  .min(5)
  // .refine(...) ← Can't add here!
  .max(10)

// ✅ v4: Freely interleave
z.string()
  .min(5)
  .refine(val => val.includes("@"))
  .max(50)
```

### New method: .overwrite()
```ts
// Transform data without changing type
z.string().overwrite(val => val.trim())
```

</v-clicks>

---

# 🎯 Enhanced Discriminated Unions

More powerful union types

<v-clicks>
```ts {all|2-9|11-17|all}
// Supports unions and pipes
const schema = z.discriminatedUnion("type", [
  z.object({
    type: z.literal("user", "admin"), // Multiple values!
    name: z.string()
  }),
  z.object({
    type: z.literal("guest")
  })
]);

// Supports nesting
const outer = z.discriminatedUnion("kind", [
  inner, // ← Another discriminated union
  z.object({ kind: z.literal("other") })
]);
```

</v-clicks>

---
layout: center
class: text-center
---

# 📊 Performance Summary

<div class="grid grid-cols-2 gap-8 mt-8">

<div v-click>
<div class="text-6xl mb-4">14×</div>
<div class="text-xl">String Parsing Speed</div>
</div>

<div v-click>
<div class="text-6xl mb-4">100×</div>
<div class="text-xl">TS Compilation</div>
</div>

<div v-click>
<div class="text-6xl mb-4">-85%</div>
<div class="text-xl">Bundle Size (Mini)</div>
</div>

<div v-click>
<div class="text-6xl mb-4">9/10</div>
<div class="text-xl">Top Issues Resolved</div>
</div>

</div>

---
layout: center
class: text-center
---

# 🎉 Summary

<v-clicks>

- ⚡️ **Performance**: 7-14x improvements across the board
- 📦 **Size**: 57% smaller core, 85% with Mini
- 🆕 **Features**: Recursive objects, template literals, file validation
- 🔧 **DX**: Faster compilation, better error messages
- 🌐 **Ecosystem**: JSON Schema, i18n, extensibility

<div class="mt-8">
Documentation: <a href="https://zod.dev" target="_blank">zod.dev</a>
</div>

</v-clicks>

---
layout: end
---

# Thanks!

Migration Guide: [zod.dev/v4/changelog](https://zod.dev/v4/changelog)

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
---

# Thank You!

## Q&A

<div class="pt-12 text-sm opacity-75">

📚 References

[Zod Documentation](https://zod.dev) ·
[GitHub](https://github.com/colinhacks/zod) ·
[TypeScript Handbook](https://www.typescriptlang.org/docs/)

</div>
