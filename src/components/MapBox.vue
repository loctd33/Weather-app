<template>
  <iframe
    width="350"
    height="200"
    frameborder="0" style="border:0;  filter: grayscale(100%) invert(92%) contrast(83%)"
    referrerpolicy="no-referrer-when-downgrade"
    :src="'https://www.google.com/maps/embed/v1/place?key='+map_api_key+'&q='+checkQuery+'&maptype=satellite&zoom=7'"
    allowfullscreen>
  </iframe>
</template>

<script>
export default {
  name: 'MapBox',
  data () {
    return {
      map_api_key: 'AIzaSyDc7PnOq3Hxzq6dxeUVaY8WGLHIePl0swY',
    }
  },
  props: {
    msg: String,
    query: String
  },
  computed: {
    // computed property that auto-updates when the prop changes
    checkQuery() {
      return this.query === "" ? "Da Nang" : this.query ;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
