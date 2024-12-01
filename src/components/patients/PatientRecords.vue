<template>
  <div>
    <section class="patient-records">
      <header class="header">
        <!-- Added label for search input with "sr-only" class to ensure screen readers can access it while keeping it visually hidden -->
        <label for="searchInput" class="sr-only">Search for patients</label>
        
        <!-- aria-label improves accessibility by providing a description of the input field for screen readers -->
        <input 
          id="searchInput"
          type="text"
          v-model="searchQuery"
          placeholder="Search patients..."
          aria-label="Search patients"
        />
      </header>

      <!-- Added role="list" for better semantic understanding of the list structure for screen readers -->
      <div class="records-list" role="list" aria-label="Patient list">
        <article 
          v-for="patient in patients" 
          :key="patient.id"
          class="patient-card"
          @click="selectPatient(patient)"
          role="listitem"
          tabindex="0"
          :aria-labelledby="'patient-' + patient.id"
        >
          <header class="patient-info" id="'patient-' + patient.id">
            <h2 class="name">{{ patient.name }}</h2>
            <div class="details">
              <span>ID: {{ patient.id }}</span>
              <span>Age: {{ patient.age }}</span>
            </div>
          </header>
        </article>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { usePatients } from '@/composables/usePatients'

const { fetchPatients } = usePatients()
const patients = ref([])
const searchQuery = ref('')

// Fetching patient records on mount
onMounted(async () => {
  try {
    patients.value = await fetchPatients()
  } catch (error) {
    console.error('Failed to fetch patients:', error)
  }
})
</script>
