<template>
  <div id='app'>
    <div class="dropzone-wrapper" v-if="!isLoading">
      <input id='dropzone' type='file' @change="changeFile" />
      <hr class="hr">
    </div>
    <!-- Hash Viewer -->
    <loader v-if="isLoading"></loader>
    <hash-viewer v-if="fileSelected && !isLoading" :hashes="hashes"></hash-viewer>
  </div>
</template>

<script>
import hasha from 'hasha'
import Loader from './components/Loader'
import HashViewer from './components/HashViewer'

export default {
  name: 'file-hash-calculator',
  components: {
    Loader,
    HashViewer
  },
  data: function () {
    return {
      fileSelected: false,
      isLoading: false,
      algorithms: ['md5', 'sha1', 'sha256', 'sha512'],
      hashes: []
    }
  },
  methods: {
    changeFile ({ type, target }) {
      const files = target.files
      this.fileSelected = files.length > 0
      if (!this.fileSelected) {
        return
      }
      this.isLoading = true
      this.hashes = []
      const targetFile = files[0]
      const promises = []
      this.algorithms.forEach((algorithm, index) => {
        promises.push(hasha.fromFile(targetFile.path, { algorithm: algorithm }))
      })

      Promise.all(promises)
        .catch((error) => {
          console.error(error)
        })
        .then((data) => {
          data.forEach((hash, index) => {
            this.hashes.push({
              algorithm: this.algorithms[index],
              value: hash
            })
          })
          this.isLoading = false
        })
    }
  }
}
</script>

<style lang="scss">
@font-face {
  font-family: 'Open Sans';
  src: url(./assets/OpenSans-Regular.ttf);
}

html, body {
  background-color: tomato;
}

#app {
  font-family: 'Open Sans';
}

.dropzone-wrapper {
  margin-top: 10px;
  margin-bottom: 20px;
  text-align: center;
}
.dropzone-wrapper:hover input[type='file'] {
  background-color: rgba(255,99,71, 0.6);
}

.hr {
  background-color: #fff;
}

input[type='file'] {
    border: 3px dashed tomato;
    padding: 140px 50px 10px 50px;
    cursor: pointer;
    position:relative;
}

input[type='file']:before {
    content: '파일을 선택하세요';
    display: block;
    position: absolute;
    text-align: center;
    top: 50%;
    left: 50%;
    width: 200px;
    height: 40px;
    margin: -25px 0 0 -100px;
    font-size: 18px;
    font-weight: bold;
}

input[type='file']:active, input[type='file']:focus {
  outline: none;
}

#app {
  width: 100%;
  overflow: hidden;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #3b405b;
  color: #fff;
}
</style>
