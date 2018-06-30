<template>
  <div id="app">

    <todo-list/>
  </div>
</template>

<script>
import TodoList from '@/components/TodoList'

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style>

@import "normalize.css";


button,
input,
optgroup,
select,
textarea,html input[type="button"],
input[type="reset"],
input[type="submit"],button[disabled],
html input[disabled],button::-moz-focus-inner,
input::-moz-focus-inner, input[type="checkbox"],
input[type="radio"], input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button, input[type="search"], input[type="search"]::-webkit-search-cancel-button,
input[type="search"]::-webkit-search-decoration {
    background-image:none;
    box-shadow: none;
    border: none;
    overflow: visible;
    margin: 0;
    padding: 0;
}

input[type="checkbox"] {
  cursor: pointer;
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  outline: 0;
  background: lightgray;
  height: 16px;
  width: 16px;
  border: 1px solid white;
}

input[type="checkbox"]:checked {
  background: #00e676;
}

input[type="checkbox"]:hover {
  filter: brightness(90%);
}

input[type="checkbox"]:disabled {
  background: #e6e6e6;
  opacity: 0.6;
  pointer-events: none;
}

input[type="checkbox"]:after {
  content: '';
  position: relative;
  left: 40%;
  top: 20%;
  width: 15%;
  height: 40%;
  border: solid #fff;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
  display: none;
}

input[type="checkbox"]:checked:after {
  display: block;
}

input[type="checkbox"]:disabled:after {
  border-color: #7b7b7b;
}





@import "https://fonts.googleapis.com/css?family=Roboto";

body {
  font-family: 'Roboto', sans-serif;
}

</style>
