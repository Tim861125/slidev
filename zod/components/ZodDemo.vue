<script setup lang="ts">
import { ref, computed } from 'vue'
import { z } from 'zod'

// Define the User schema
const UserSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  age: z.number().min(0, 'Age must be positive').max(150, 'Age must be realistic')
})

// Form inputs
const name = ref('')
const age = ref('')

// Validation results
const validationResult = ref<{ success: boolean; error?: string; data?: any } | null>(null)

// Validate with TypeScript type assertion (without Zod)
const validateWithoutZod = () => {
  try {
    const data = {
      name: name.value,
      age: age.value ? Number(age.value) : age.value
    } as { name: string; age: number }

    validationResult.value = {
      success: true,
      data: data,
      error: 'TypeScript allows this! But runtime type is NOT guaranteed.'
    }
  } catch (error: any) {
    validationResult.value = {
      success: false,
      error: error.message
    }
  }
}

// Validate with Zod
const validateWithZod = () => {
  const inputData = {
    name: name.value,
    age: age.value ? Number(age.value) : age.value
  }

  const result = UserSchema.safeParse(inputData)

  if (result.success) {
    validationResult.value = {
      success: true,
      data: result.data
    }
  } else {
    validationResult.value = {
      success: false,
      error: result.error.errors.map(e => `${e.path.join('.')}: ${e.message}`).join('\n')
    }
  }
}

// Clear form
const clearForm = () => {
  name.value = ''
  age.value = ''
  validationResult.value = null
}

// Preset invalid data
const setInvalidData = () => {
  name.value = ''
  age.value = '-5'
  validationResult.value = null
}

// Preset valid data
const setValidData = () => {
  name.value = 'John Doe'
  age.value = '25'
  validationResult.value = null
}
</script>

<template>
  <div class="zod-demo">
    <div class="demo-container">
      <!-- Input Section -->
      <div class="input-section">
        <h3 class="section-title">User Input</h3>

        <div class="form-group">
          <label for="name">Name:</label>
          <input
            id="name"
            v-model="name"
            type="text"
            placeholder="Enter name"
            class="input-field"
          />
        </div>

        <div class="form-group">
          <label for="age">Age:</label>
          <input
            id="age"
            v-model="age"
            type="text"
            placeholder="Enter age"
            class="input-field"
          />
        </div>

        <div class="button-group">
          <button @click="setValidData" class="btn btn-secondary">Valid Data</button>
          <button @click="setInvalidData" class="btn btn-secondary">Invalid Data</button>
          <button @click="clearForm" class="btn btn-secondary">Clear</button>
        </div>
      </div>

      <!-- Validation Section -->
      <div class="validation-section">
        <h3 class="section-title">Validation</h3>

        <div class="button-group">
          <button @click="validateWithoutZod" class="btn btn-danger">
            ❌ Without Zod
          </button>
          <button @click="validateWithZod" class="btn btn-success">
            ✅ With Zod
          </button>
        </div>

        <!-- Result Display -->
        <div v-if="validationResult" class="result-box" :class="{ success: validationResult.success, error: !validationResult.success }">
          <h4>{{ validationResult.success ? '✅ Success' : '❌ Validation Failed' }}</h4>

          <div v-if="validationResult.success" class="result-content">
            <p><strong>Validated Data:</strong></p>
            <pre>{{ JSON.stringify(validationResult.data, null, 2) }}</pre>
            <p v-if="validationResult.error" class="warning">⚠️ {{ validationResult.error }}</p>
          </div>

          <div v-else class="result-content">
            <p><strong>Errors:</strong></p>
            <pre>{{ validationResult.error }}</pre>
          </div>
        </div>
      </div>
    </div>

    <!-- Schema Display -->
    <div class="schema-display">
      <h3 class="section-title">Schema Definition</h3>
      <pre class="code-block">const UserSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  age: z.number()
    .min(0, 'Age must be positive')
    .max(150, 'Age must be realistic')
})</pre>
    </div>
  </div>
</template>

<style scoped>
.zod-demo {
  padding: 0.5rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 8px;
  color: white;
  font-size: 0.75rem;
  max-height: 80vh;
  overflow: auto;
}

.demo-container {
  display: grid;
  grid-template-columns: 1fr 1.2fr;
  gap: 0.6rem;
  margin-bottom: 0.6rem;
}

.section-title {
  font-size: 0.85rem;
  font-weight: 600;
  margin-bottom: 0.4rem;
  color: #ffd700;
}

.input-section,
.validation-section {
  background: rgba(255, 255, 255, 0.1);
  padding: 0.6rem;
  border-radius: 6px;
  backdrop-filter: blur(10px);
}

.form-group {
  margin-bottom: 0.4rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.2rem;
  font-weight: 500;
  font-size: 0.7rem;
}

.input-field {
  width: 100%;
  padding: 0.35rem 0.45rem;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 4px;
  background: rgba(255, 255, 255, 0.15);
  color: white;
  font-size: 0.75rem;
  transition: all 0.3s ease;
}

.input-field:focus {
  outline: none;
  border-color: #ffd700;
  background: rgba(255, 255, 255, 0.25);
}

.input-field::placeholder {
  color: rgba(255, 255, 255, 0.5);
}

.button-group {
  display: flex;
  gap: 0.3rem;
  flex-wrap: wrap;
  margin-top: 0.4rem;
}

.btn {
  padding: 0.35rem 0.6rem;
  border: none;
  border-radius: 4px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.65rem;
}

.btn:hover {
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.btn-secondary {
  background: rgba(255, 255, 255, 0.2);
  color: white;
}

.btn-success {
  background: #48bb78;
  color: white;
}

.btn-danger {
  background: #f56565;
  color: white;
}

.result-box {
  margin-top: 0.6rem;
  padding: 0.6rem;
  border-radius: 5px;
  animation: slideIn 0.3s ease;
  max-height: 180px;
  overflow-y: auto;
}

.result-box.success {
  background: rgba(72, 187, 120, 0.2);
  border: 1.5px solid #48bb78;
}

.result-box.error {
  background: rgba(245, 101, 101, 0.2);
  border: 1.5px solid #f56565;
}

.result-box h4 {
  margin: 0 0 0.4rem 0;
  font-size: 0.8rem;
}

.result-content pre {
  background: rgba(0, 0, 0, 0.3);
  padding: 0.4rem;
  border-radius: 4px;
  overflow-x: auto;
  font-size: 0.65rem;
  line-height: 1.3;
  margin: 0;
}

.result-content p {
  margin: 0.25rem 0;
  font-size: 0.7rem;
}

.warning {
  margin-top: 0.4rem;
  padding: 0.4rem;
  background: rgba(255, 193, 7, 0.2);
  border-left: 2px solid #ffc107;
  border-radius: 3px;
  font-size: 0.65rem;
}

.schema-display {
  background: rgba(255, 255, 255, 0.1);
  padding: 0.6rem;
  border-radius: 6px;
  backdrop-filter: blur(10px);
}

.code-block {
  background: rgba(0, 0, 0, 0.4);
  padding: 0.6rem;
  border-radius: 4px;
  overflow-x: auto;
  font-size: 0.65rem;
  line-height: 1.4;
  border-left: 3px solid #ffd700;
  margin: 0;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
