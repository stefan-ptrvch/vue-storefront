<template>
  <div>
    {{ response }}
  </div>
</template>

<script>
export default {
  data () {
    return {
      response: String
    }
  },
  mounted () {
    var invocation = new XMLHttpRequest()
    var url = 'https://tools.keycdn.com/geo.json?host=35.198.187.67'

    function callOtherDomain () {
      if (invocation) {
        invocation.open('GET', url, true)
        invocation.onreadystatechange = () => {
          console.log('asd')
        }
        invocation.send()
      }
    }
    callOtherDomain()
  }
}
</script>
