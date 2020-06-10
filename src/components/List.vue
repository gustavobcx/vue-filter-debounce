<template>
  <div class="container">
    <div class="input-container">
      <input type="search" id="search" @input="debounceInput" required />
      <label for="search">Search</label>
    </div>
    <ul>
      <li class="item" :key="item.id" v-for="item of filtered">
        <img loading="lazy" :src="img(item.title)" />
        <span>{{ item.title }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import debounce from 'debounce';

export default {
  name: 'List',
  data() {
    return {
      filter: '',
      items: []
    }
  },
  computed: {
    filtered() {
      if (this.filter) {
        let exp = new RegExp(this.filter.trim(), 'i');
        return this.items.filter(item => exp.test(item.title));
      }
      return this.items;
    }
  },
  methods: {
    img(str) {
      return `https://api.adorable.io/avatars/52/abott@${str}.png`;
    }
  },
  created() {
    this.debounceInput = debounce(e => this.filter = e.target.value, 500);

    const data = sessionStorage.getItem('data');

    if (data) return this.items = JSON.parse(data);

    fetch('https://jsonplaceholder.typicode.com/posts')
      .then(r => r.json())
      .then(d => {
        sessionStorage.setItem('data', JSON.stringify(d));
        this.items = d;
      });
  }
}
</script>

<style scoped>
ul {
  margin-top: 20px;
}

li {
  list-style: none;
}

.container {
  max-width: 637px;
  margin: 8px auto;
  padding: 16px;
  background: #fff;
  box-shadow: 0 2px 2px 0 rgba(0,0,0,0.14), 0 1px 5px 0 rgba(0,0,0,0.12), 0 3px 1px -2px rgba(0,0,0,0.2);
}

.input-container {
  position: relative;
  margin-top: 8px;
}

input {
  border: 0;
  border-bottom: 2px solid #ccc;
  outline: none;
  transition: .2s ease-in-out;
  box-sizing: border-box;
}

label {
  top: 0;
  left: 0; right: 0;
  color: #999;
  display: flex;
  align-items: center;
  position: absolute;
  font-size: 1rem;
  cursor: text;
  transition: .2s ease-in-out;
  box-sizing: border-box;
}

input,
label {
  width: 100%;
  height: 32px;
  font-size: 16px;
}

input:valid,
input:focus {
  border-bottom: 2px solid #26a69a;  
}

input:valid + label,
input:focus + label {
  color: #26a69a;
  font-size: .8rem;
  top: -24px;
  pointer-events: none;
}

.item {
  padding: 5px;
  margin-top: 16px;
  display: flex;
  align-items: center;
}

.item img {
  border-radius: 52px;
}

.item span {
  margin-left: 8px;
}

.item span:first-letter {
  text-transform: uppercase;
}

</style>