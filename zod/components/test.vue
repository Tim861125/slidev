<template></template>
<script>
const schema = z.string();
import { z } from "zod";
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
</script>
