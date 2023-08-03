<template>
    <dialog open id="loadafile">
      <h2 style="color: #fff; text-align: center;">WEB Local video trimmer/editor</h2>
      <input type="file" id="video-input" ref="videoInput" @change="loadVideo" accept="video/*" />
      <input type="text" id="url-input" placeholder="Enter video URL" ref="urlInput" />
      <button @click="loadVideo">Load</button>
    </dialog>
    <editor v-if="selectedVideo" :video-url="selectedVideo" />
  </template>
  
  <script>
  export default {
    data() {
      return {
        selectedVideo: null,
      };
    },
    methods: {
      loadVideo() {
        const url = this.$refs.urlInput.value;
        if (url) {
          this.selectedVideo = url;
        } else {
          const file = this.$refs.videoInput.files[0];
          const fileURL = URL.createObjectURL(file);
          this.selectedVideo = fileURL;
        }
        this.hideLoadFile();
        this.showEditorUI();
      },
      hideLoadFile() {
        const loadFileElement = document.getElementById('loadafile');
        if (loadFileElement) {
          loadFileElement.style.display = 'none';
        }
      },
      showEditorUI() {
        const editorUIElement = document.getElementById('editorUI');
        if (editorUIElement) {
          editorUIElement.style.display = 'block';
        }
      },
    },
  };
  </script>
<style>
dialog {
    background-color: #000;
}

body {
    background-color: #000;
    margin: 0;
    padding: 0;
}

#loadafile {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: calc(100vh - 20px);
    padding: 20px;
    box-sizing: border-box;
}

input[type="file"],
input[type="text"],
button {
    font-size: 16px;
    padding: 10px;
    border: none;
    background-color: #222;
    color: #fff;
    margin: 10px 0;
    border-radius: 4px;
    width: 100%;
    max-width: 400px;
    box-sizing: border-box;
    text-align: center;
}

button {
    cursor: pointer;
    transition: all 0.3s ease-in-out;
}

button:hover {
    background-color: #fff;
    color: #222;
}
dialog::backdrop {
    background-color: salmon;
    }
</style>