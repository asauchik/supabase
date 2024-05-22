<script setup>
import { ref, onMounted } from 'vue';
import { createClient } from '@supabase/supabase-js';

const supabase = createClient('https://wltvzryjoetvyoearuzj.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndsdHZ6cnlqb2V0dnlvZWFydXpqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MTYyNDI2MTEsImV4cCI6MjAzMTgxODYxMX0.DtHiOIk89aBn9xFSrsYAA0NyJWFAgL-yqlLZKOJh3Fo');

const Apps = ref([]);
const country_name = ref(''); 
const abbreviation = ref(''); 

async function getApps() {
  try {
    const { data, error } = await supabase.from('Apps').select();
    if (error) throw error;
    Apps.value = data;
    console.log('countries loaded:', Apps.value);
  } catch (error) {
    console.error('Error loading Apps:', error);
  }
}

async function insert() {
  if (!country_name.value || !abbreviation.value) {
    // Display message popup
    console.warn('Please fill in both Country Name and Abbreviation');
    return; // Prevent form submission if fields are empty
  }

  try {
    const { error } = await supabase
      .from('countries')
      .insert({ name: country_name.value, abbreviation: abbreviation.value });

    if (error) throw error;

    console.log('Data inserted successfully');
    country_name.value = '';
    abbreviation.value = '';
    getCountries();
  } catch (error) {
    console.error('Error inserting data:', error);
  }
}

onMounted(() => {
  getApps();
});
</script>

<template>

     <Ucontainer class="shadow-xl">
      <UTable 
      loading
    :loading-state="{ icon: 'i-heroicons-arrow-path-20-solid', label: 'Loading...' }"
    :progress="{ color: 'primary', animation: 'carousel' }"
    class="w-full"
      :rows="Apps" :columns="[ 
         { key: 'application_number', label: 'Application Number' },
  { key: 'first_name', label: 'First Name' }

]"/></Ucontainer>

<UContainer class="shadow-l">
<div class="flex items-center mb-4" > 
  
  <UInput
      v-model="country_name"
      name="country_name"
      placeholder="Country Name..."
      icon="i-heroicons-arrow-right-circle"
      autocomplete="off"
      :ui="{ icon: { trailing: { pointer: '' } } }"
      class="flex-grow mr-2"  />
      
    <UInput
      v-model="abbreviation"
      name="abbreviation"
      placeholder="Abbreviation..."
      icon="i-heroicons-arrow-right-circle"
      :ui="{ icon: { trailing: { pointer: '' } } }"
    />
  </div>
      <UContainer class="p-3 bg-yellow-50"><UButton @click="insert" color="primary" variant="solid">Button</UButton></UContainer>
    </UContainer>
</template>
