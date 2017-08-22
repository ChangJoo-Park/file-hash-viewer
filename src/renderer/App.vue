<template>
  <div id='app'>
    <div class="dropzone-wrapper">
      <input id='dropzone' type='file' />
    </div>
    <div class="loading-indicator" v-if="isLoading">
      LOADING
    </div>
    <!-- Hash Viewer -->
    <div id="hash-viewer" v-if="fileSelected && !isLoading">
      <ul>
        <li v-for="hash in hashes" :key="hash.value">
          <strong>{{hash.algorithm}}</strong> - {{hash.value}}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import hasha from 'hasha'

export default {
  name: 'file-hash-calculator',
  mounted: function () {
    document.getElementById('dropzone').addEventListener('change', (e) => {
      const files = e.target.files
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
    })
  },
  beforeDestroy: () => {
    document.getElementById('dropzone').removeEventListener('change')
  },
  data: function () {
    return {
      maxSize: 100,
      fileSelected: false,
      isLoading: false,
      algorithms: ['md5', 'sha1', 'sha256', 'sha512'],
      hashes: []
    }
  },
  methods: {
  }
}
</script>

<style lang="scss">
  /* CSS */
.dropzone-wrapper {
  margin-top: 50px;
  text-align: center;
}
input[type='file'] {
    border: 3px dashed tomato;
    padding: 140px 50px 10px 50px;
    cursor: move;
    position:relative;
}
input[type='file']:before {
    content: 'drag & drop your files here';
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
    color: tomato;
}
</style>
