<script setup>
const name = ref('')
const email = ref('')
const age = ref(null)
const phonenumber = ref(null)
const step = ref(1)

const route = useRoute()
const router = useRouter()

onMounted(() => {
  const savedData = localStorage.getItem('form_data')
  if (savedData) {
    const parsed = JSON.parse(savedData)
    name.value = parsed.name || ''
    email.value = parsed.email || ''
    age.value = parsed.age || null
    phonenumber.value = parsed.phonenumber || null
  }

  if (route.query.step) {
    step.value = parseInt(route.query.step)
  }
})

watch([name, email, age, phonenumber, step], () => {
  const dataToSave = {
    name: name.value,
    email: email.value,
    age: age.value,
    phonenumber: phonenumber.value
  }
  localStorage.setItem('form_data', JSON.stringify(dataToSave))

  router.push({ query: { step: step.value } })
})

function nextStep() { step.value++ }
function prevStep() { step.value-- }

function submit() {
  alert("Form Submitted Successfully!")
  
  localStorage.removeItem('form_data')

  name.value = ''
  email.value = ''
  age.value = null
  phonenumber.value = null

  step.value = 1

  router.push({ query: {} })
}

const isStep1Valid = () => name.value.length > 2
const isStep2Valid = () => email.value.includes('@') && email.value.includes('.com')
const isStep3Valid = () => age.value >= 18 && age.value <= 100
const isStep4Valid = () => String(phonenumber.value || '').length === 10
</script>

<template>
  <div class="form-container">
    <div v-if="step === 1" class="card">
      <h2>Step 1: Your Name</h2>
      <input v-model="name" placeholder="Full Name" />
      <p v-if="name && !isStep1Valid()" class="error">Name too short</p>
      <div class="actions">
        <button :disabled="!isStep1Valid()" @click="nextStep">Next</button>
      </div>
    </div>

    <div v-else-if="step === 2" class="card">
      <h2>Step 2: Email Address</h2>
      <input v-model="email" type="email" placeholder="email@example.com" />
      <p v-if="email && !isStep2Valid()" class="error">Email should contain @ and .com</p>
      <div class="actions">
        <button @click="prevStep">Back</button>
        <button :disabled="!isStep2Valid()" @click="nextStep">Next</button>
      </div>
    </div>

    <div v-else-if="step === 3" class="card">
      <h2>Step 3: Age</h2>
      <input v-model.number="age" type="number" placeholder="Enter age" />
      <p v-if="age && !isStep3Valid()" class="error">Must be between 18-100</p>
      <div class="actions">
        <button @click="prevStep">Back</button>
        <button :disabled="!isStep3Valid()" @click="nextStep">Next</button>
      </div>
    </div>

    <div v-else-if="step === 4" class="card">
      <h2>Step 4: Phone Number</h2>
      <input v-model.number="phonenumber" type="number" placeholder="Enter Your Phone Number" />
      <p v-if="phonenumber && !isStep4Valid()" class="error">Must be 10 digits</p>
      <div class="actions">
        <button @click="prevStep">Back</button>
        <button :disabled="!isStep4Valid()" @click="nextStep">Next</button>
      </div>
    </div>

    <div v-else-if="step === 5" class="card">
      <h2>Step 5: Review</h2>
      <div class="summary">
        <p><strong>Name:</strong> {{ name }}</p>
        <p><strong>Email:</strong> {{ email }}</p>
        <p><strong>Age:</strong> {{ age }}</p>
        <p><strong>Phone:</strong> {{ phonenumber }}</p>
      </div>
      <div class="actions">
        <button @click="prevStep">Back to Edit</button>
        <button class="submit-btn" @click="submit">Submit</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.form-container { max-width: 400px; margin: 50px auto; font-family: sans-serif; }
.card { border: 1px solid #ddd; padding: 20px; background: #fff; }
.actions { margin-top: 20px; display: flex; gap: 10px; }
input { width: 100%; padding: 12px; margin-top: 5px; box-sizing: border-box; border: 1px solid #ccc; border-radius: 4px; }
.error { color: #e74c3c; font-size: 12px; margin-top: 5px; }
.submit-btn { background-color: #42b883; color: white; border: none; padding: 10px; flex: 1; border-radius: 4px; cursor: pointer; }
button { padding: 10px 20px; cursor: pointer; border: 1px solid #ccc; border-radius: 4px; background: #f4f4f4; }
button:disabled { opacity: 0.5; cursor: not-allowed; }
.summary p { margin: 8px 0; border-bottom: 1px solid #eee; padding-bottom: 4px; }
</style>