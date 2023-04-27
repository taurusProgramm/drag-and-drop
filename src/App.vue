<template>
  <div class="box" :class="{'box-ondrop':isDragStarted}">
    <input type="file" 
      id="file" 
      multiple 
      accept=".png, .jpg, .jpeg"
      ref="inputFile"
      @change="uploadFile"
      @dragenter="isDragStarted = true"
      @dragleave="isDragStarted = false"
      
    >
    <label for="file"> {{ isDragStarted ?  'Отпустите, чтобы сохранить' : 'Выберите фото'}} </label>
  </div>
  <div class="present"
    v-if="files.length"
  >
    <div class="file" 
      v-for="(file) in files" 
      :key="file.name"
      @click="modalOpenFunc(file.name)"
    >
        <img :src="getSrc(file)" :alt="file.name">
        <div class="remove-btn" @click.stop="removeItem(file.name)">&times;</div>
        <div class="file-info">
          <span>{{ file.name }}</span>
          <span>{{ byteToSize(file.size) }}</span>
        </div>
    </div>
  </div>

        <teleport to='body'>
            <div class="modal"
              v-if="modalOpen"
            >
              <div class="photo-wrapper">
                <img :src="srcForModal" alt="фото">
              </div>
              <div class="close-modal-btn" @click="modalOpen = false">&times;</div>
            </div>
        </teleport>

        

</template>

<script setup lang="ts">
import { ref } from 'vue';

  const inputFile = ref<HTMLInputElement | null>(null)
  const files = ref<Array<File>>([])
  const isDragStarted = ref(false)
  const modalOpen = ref(false)
  const srcForModal = ref('')
  

  const uploadFile = (e:Event)=>{
    const target = e.target as HTMLInputElement
    
    console.debug(target.files)
    files.value = [...files.value, ...Array.from(target.files ?? [])]

    if(inputFile.value) {
      inputFile.value.value = ''
    }
    isDragStarted.value = false
    
  }


  const getSrc = (file: File) => URL.createObjectURL(file)

  // const getSrc2= (file: File) => {
  //   const reader = new FileReader()
  //   reader.onload = ev => {
  //     console.log(ev.target?.result);
  //     scr2.value = ev.target?.result as string
  //   }
  //   reader.readAsDataURL(file)
  // }

  const removeItem = ( name: string) => {
    files.value = files.value.filter(i => i.name !== name)
  } 
  
  const modalOpenFunc = (name: string) => {
    const [file] = files.value.filter(i => i.name === name)
   srcForModal.value = getSrc(file)
    modalOpen.value = true
  }

 
  function byteToSize(bytes: number): string{
    const sizes = ['Bytes', 'Kb', 'Mb', 'Gb', 'Tb'];
    if(!bytes) return '0 Byte';
    const i = Math.floor(Math.log(bytes) / Math.log(1024))
    return Math.round(bytes / Math.pow(1024, i)) + ' ' + sizes[i]
  }

</script>

<style>

*{
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  max-width: 100vw;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  font-size: 25px;
  background: #c9c9ca;
  overflow-x: hidden;
}

.drop-area{
  height: 500px;
  width: 500px;
  border: 5px dashed black;
  display: flex;
  justify-content: center;
  align-items: center;
}

.box {
  position: relative;
  height: 200px;
  width: 400px;
  border: 4px dotted black;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #fff;
  margin-top: 10px;
}

.box.box-ondrop {
  border: 6px dotted greenyellow;
}

label {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  cursor: pointer;
}
input[type=file]{
  opacity: 0;
  position: absolute;
  inset: 0;
}

.present{
  width: 600px;
  min-height: 300px;
  background: #fff;
  margin-top: 25px;
  padding: 15px;
  font-size: 20px;
  border-radius: 10px;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}

.file {
  position: relative;
  margin-left: 10px;
  margin-bottom: 10px;
  overflow: hidden;
  
}

.file:hover .remove-btn {
  opacity: 1;
}

.file img {
  width: 140px;
  height: auto;
  border-radius: 5px;
  display: block;
}

.file .remove-btn {
  position: absolute;
  top: 0;
  right: 0;
  height: 25px;
  width: 25px;
  background: rgba(255, 255, 255, .5);
  border-bottom-left-radius: 5px;
  font-size: 27px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  opacity: 0;
  transition: opacity .22s;
}

.file .file-info{
  position: absolute;
  bottom: -50px;
  left: 0;
  right:0;
  min-height: 35px;
  background: rgba(255, 255, 255, .5);
  padding: 0 5px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  transition: bottom .22s;
}

.file:hover .file-info {
  bottom: 0;
}

.file .file-info > span{
  font-size: 13px;
}

.modal {
  position: absolute;
  inset: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(0,0,0,.5);;
}

.modal .close-modal-btn{
  position: absolute;
  top: 0;
  right: 0;
  font-size: 40px;
  height: 60px;
  width: 60px;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #fff;
  cursor: pointer;
}

.modal .close-modal-btn:hover{
  background: rgba(128, 128, 128, .7);
}

.modal .photo-wrapper img{
  width: 400px;
  height: auto;
  border-radius: 20px;
}

</style>
