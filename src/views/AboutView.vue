<template>
  <div class="about">
    <form @submit.prevent="isEditing? updateData($event) : addData($event)" class="form">
      <label for="name">Name:</label>
      <input type="text" id="name" name="name" required  :value="formData.name"/>
      <label for="subject">Subject:</label>
      <input type="text" id="subject" name="subject" required :value="formData.subject"/>
      <button type="submit">Submit</button>
    </form>
    <table border="1" cellspacing="0" cellpadding="8">
      <thead>
        <tr>
          <th>Name</th>
          <th>Subject</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="value in students" :key="value.id">
          <td>{{ value.name }}</td>
          <td>{{ value.subject }}</td>
          <td>
            <button @click="deleteStudent(value.id)">Delete</button>
            <button @click="editStudent(value.id)">Edit</button>
          </td>
          
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref } from 'vue';
const students = ref([
  
]);
const formData = ref({id: null,  name: '', subject: '' });
const isEditing = ref(false);
const addData = (event) => {
  formData.value.name = event.target.elements.name.value;
  formData.value.subject = event.target.elements.subject.value;
  if (formData.value.name && formData.value.subject && !isEditing.value) {
    students.value.push({ id: Date.now(), name: formData.value.name, subject: formData.value.subject });
    formData.value.name = '';
    formData.value.subject = '';
  }

};

const updateData = (event) => {
  formData.value.name = event.target.elements.name.value;
  formData.value.subject = event.target.elements.subject.value;
  if (formData.value.name && formData.value.subject && isEditing.value) {
    const index = students.value.findIndex(student => student.id === formData.value.id);
    if (index !== -1) {
      students.value[index] = { id: formData.value.id, name: formData.value.name, subject: formData.value.subject };
    }
    formData.value.name = '';
    formData.value.subject = '';
    isEditing.value = false;
  }
};

const deleteStudent = (id) => {
  students.value = students.value.filter(student => student.id !== id);
}

const editStudent = (id) => {
  const student = students.value.find(student => student.id === id);
  if (student) {
    formData.value.name = student.name;
    formData.value.subject = student.subject;
    formData.value.id = student.id;
     isEditing.value = true;
  }
};

onMounted(async() => {
  const response = await fetch('https://jsonplaceholder.typicode.com/users');
  const data = await response.json();
  students.value = data.map(user => ({ id: user.id, name: user.name, subject: 'N/A' })); 
});

</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 2rem;
  }
  .form {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
}
</style>
