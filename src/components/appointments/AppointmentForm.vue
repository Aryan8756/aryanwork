<template>
  <form @submit.prevent="handleSubmit" class="space-y-6">
    <!-- Basic form without proper validation -->
    <div>
       <!--we can not write direct hard coreded value in code "Patient Name" we can  Use Constants in the Code-->
      <label for="patientName">Patient Name</label>
       <!-- This field is required, so the user must fill it in before submitting -->
      <input
        id="patientName"
        v-model="formData.patientName"
        type="text"
        class="form-input"
        required
      />
    </div>

    <div>
       <!-- This field is required, so the user must fill it in before submitting -->
      <label for="email">Email</label>
      <input
        id="email"
        v-model="formData.email"
        type="email"
        class="form-input"
        required
      />
    </div>

    <div>
       <!-- This field is required, so the user must fill it in before submitting -->
      <label for="phone">Phone Number</label>
      <input
        id="phone"
        v-model="formData.phone"
        type="tel"
        class="form-input"
        required
      />
    </div>

    <TimeSlotPicker v-model="formData.timeSlot" />

    <button type="submit" class="btn-primary">
      Book Appointment
    </button>
  </form>
</template>

<script setup>
import { ref } from 'vue';
import TimeSlotPicker from './TimeSlotPicker.vue';
import { appointmentService } from '../../services/appointmentService.js';

const formData = ref({
  patientName: '',
  email: '',
  phone: '',
  timeSlot: null,
});

const errors = ref({});

const validateForm = () => {
  errors.value = {};

  // Basic field validation also need always we use chaining because of this Chaining simplifies code readability, manages asynchronous tasks efficiently, and improves error handling.
  
  if (!formData?.value?.patientName) errors.value.patientName = 'Name is required.';
  if (!formData?.value?.email || !/^\S+@\S+\.\S+$/.test(formData.value.email)) {
    errors.value.email = 'Invalid email.';
  }
  if (!formData.value.phone || !/^\d{10}$/.test(formData.value.phone)) {
    errors.value.phone = 'Phone must be 10 digits.';
  }
  if (!formData.value.timeSlot) errors.value.timeSlot = 'Time slot is required.';

  return Object.keys(errors.value).length === 0;
};

const handleSubmit = async () => {
  if (!validateForm()) return;

  try {
    // Check time slot availability and book appointment
    const availability = await appointmentService.checkTimeSlotAvailability(formData.value.timeSlot);
    if (!availability.data.available) {
      errors.value.timeSlot = 'Time slot already booked.';
      return;
    }

    await appointmentService.createAppointment(formData.value);
    alert('Appointment booked successfully!');
    formData.value = { patientName: '', email: '', phone: '', timeSlot: null }; // Reset form
  } catch (error) {
    alert('Error booking appointment. Try again later.');
    console.error('Booking error:', error);
  }
};
</script>