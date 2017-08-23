<template>
  <div id="hash-viewer">
    <ul class="hash-list">
      <li v-for="hash in hashes" :key="hash.value" class="hash-item">
        <div class="hash-algorithm">
          <strong>{{hash.algorithm}}</strong>
        </div>
        <pre @dblclick="selectHash(hash.value)">{{hash.value}}</pre>
      </li>
    </ul>
  </div>
</template>

<script>
import iziToast from 'izitoast'

export default {
  props: ['hashes'],
  methods: {
    selectHash (hash) {
      try {
        var successful = document.execCommand('copy')
        if (successful) {
          iziToast.show({
            title: '클립보드에 복사되었습니다.',
            message: '',
            theme: 'light',
            timeout: 1000,
            pauseOnHover: false
          })
        }
      } catch (err) {
        console.log('Oops, unable to copy')
      }
    }
  }
}
</script>

<style>
#hash-viewer {
  margin-top: 30px;
}

.hash-list {
  margin-top: 10px;
  list-style: none;
  margin: 0;
  padding: 10px;
}

.hash-list .hash-item {
  margin-bottom: 10px;
  word-wrap: break-word;
}

.hash-algorithm {
  font-weight: bold;
  text-transform: uppercase;
  margin-bottom: 5px;
  user-select: none;
}
</style>
