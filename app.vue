<script setup>
import { ref, onMounted } from 'vue';
import { createClient } from '@supabase/supabase-js';

const supabase = createClient('https://wltvzryjoetvyoearuzj.supabase.co', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6IndsdHZ6cnlqb2V0dnlvZWFydXpqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3MTYyNDI2MTEsImV4cCI6MjAzMTgxODYxMX0.DtHiOIk89aBn9xFSrsYAA0NyJWFAgL-yqlLZKOJh3Fo');

const countries = ref([]);
const country_name = ref(''); 
const abbreviation = ref(''); 

async function getCountries() {
  try {
    const { data, error } = await supabase.from('countries').select();
    if (error) throw error;
    countries.value = data;
    console.log('countries loaded:', countries.value);
  } catch (error) {
    console.error('Error loading applications:', error);
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
  getCountries();
});
</script>

<template>
  <div class="grid grid-cols-3 gap-4">
    <div></div>
    <div>
      <UTable :rows="countries" :columns="[ 
         { key: 'name', label: 'Country Name' },
  { key: 'abbreviation', label: 'Abbreviation' }

]"/>
<div class="flex items-center mb-4" >  <UInput
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
      <UButton @click="insert" color="primary" variant="solid">Button</UButton>
    </div>
    <div></div>
  </div>
</template>
