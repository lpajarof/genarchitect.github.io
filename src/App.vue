<template>
  <div>
    <div class="row">
      <div class="col-md-4">
        <div class="row">
          <div class="form-group">
            <label for="textArea"><h3>Requerimientos a Chat GPT</h3></label>
            <textarea v-model="text_gpt" id="requirements" class="form-control spacing"></textarea>
          </div>
        </div>
        <div class="row">
          <div class="col">
            <button @click="submit_gpt" class="btn btn-success spacing">Prompt Eraser</button>
          </div>
          <div class="col">
            <div class="form-check">
              <input class="form-check-input" type="radio" name="exampleRadios" id="exampleRadios1" value="es" v-model="selectedLanguage" checked>
              <label class="form-check-label" for="exampleRadios1"> Español </label>              
            </div>
            <div class="form-check">
              <input class="form-check-input" type="radio" name="exampleRadios" id="exampleRadios2" value="en" v-model="selectedLanguage">
              <label class="form-check-label" for="exampleRadios2"> Inglés </label>              
            </div>
          </div>
          <div class="col">
            <button @click="clear_req" class="btn btn-danger spacing">Limpiar</button>
          </div>
        </div>
        <div class="row">
          <hr>
        </div>
        <div class="row">          
          <div class="form-group">
            <label for="textArea"><h3>Prompt para Eraser</h3></label>
            <textarea v-model="text" id="textArea" class="form-control spacing"></textarea>
          </div>        
        </div>
        <div class="row">
          <div class="col">
            <button @click="submit_era" class="btn btn-success spacing">Crear Arqu.</button>
          </div>
          <div class="col">
            <button @click="clear_era" class="btn btn-danger spacing">Limpiar</button>
          </div>
        </div>
      </div>
      <div class="col-md-8 d-flex justify-content-center align-items-center rounded">
        <div>{{ message }}</div>
        <img :src="imageUrl" alt="Image" v-if="imageUrl">
        <div v-if="isLoading">
          <div>
            <h2>Loading...</h2>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.rounded {
  border-radius: 25px;
  background-color: rgb(210, 226, 243);
}
</style>
<script>
import axios from 'axios';

export default {
  data() {
    return {
      text: '',
      text_gpt: '',
      items: ['item1', 'item2', 'item3'],
      selectedItem: '',
      originalItems: ['item1', 'item2', 'item3'],
      imageUrl: '',
      message: '',
      isLoading: false,
      selectedLanguage: 'es'
    };
  },
  methods: {
      async submit_gpt() {
      
      try {
        var idioma = " ";
        if (this.selectedLanguage == 'es'){
          idioma = " "
        }        
        else{
          idioma = " Traduce al inglés:"
        }
        
        const response_gpt = await axios.post('http://127.0.0.1:5000/api/chatgpt', { text: (idioma + this.text_gpt ) });
        this.text = response_gpt.data;      

      } catch (error) {
        this.message = 'Error: ' + error.message;
      }
      this.isLoading = false;
    },
    async submit_era() {
      this.imageUrl = '';
      this.isLoading = true;
      const diagramType_ = "cloud-architecture-diagram";
      const background_ = false;
      const theme_ = "light";
      const scale_ = "1"
      const returnFile_ = false;
      try {
        
        const response = await axios.post('http://127.0.0.1:5000/api/render/prompt', { text: this.text, diagramType: diagramType_, background: background_, theme: theme_, scale: scale_, returnFile: returnFile_ });

        if (response.data['imageUrl'] != null){
          this.message = null;
          this.imageUrl = response.data['imageUrl'];
        }
        else{
          this.message = 'Error: No se pudo generar la imagen.';
          this.imageUrl = '';
        }        


      } catch (error) {
        this.message = 'Error: ' + error.message;
      }
      this.isLoading = false;
    },
    clear_req() {     
      this.text_gpt = ''; 
      this.message = '';  
    },
    clear_era() {
      this.text = '';
      this.message = '';      
    }
  }
}
</script>
