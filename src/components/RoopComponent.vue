<template>
    <div>
      <label for="roopActive">
        <input type="checkbox" id="roopActive" v-model="roopActive">
        Enable Roop
      </label>
      
      <label for="roopImage">Roop Image:</label>
      <input type="file" id="roopImage" @change="handleFileUpload">
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        roopActive: false,
        roopImage: null
      };
    },
    methods: {
      handleFileUpload(event) {
        const file = event.target.files[0];
        const reader = new FileReader();
  
        reader.onload = (e) => {
          this.roopImage = e.target.result;
        };
  
        reader.readAsDataURL(file);
      }
    }
  }
  </script>
  