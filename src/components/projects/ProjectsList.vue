<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search
      v-if="hasProjects"
      @search="updateSearch"
      :search-term="enteredSearchTerm"
    ></base-search>
    <ul v-if="hasProjects">
      <project-item
        v-for="prj in availableProjects"
        :key="prj.id"
        :title="prj.title"
      ></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script setup>
import { ref, computed, watch, defineProps, toRefs } from 'vue';

import ProjectItem from './ProjectItem.vue';

// ______________________________________________________________________
const props = defineProps(['user']);
const enteredSearchTerm = ref('');
const activeSearchTerm = ref('');

// ______________________________________________________________________
const availableProjects = computed(() => {
  if (activeSearchTerm.value) {
    return props.user.projects.filter((prj) =>
      prj.title.includes(activeSearchTerm.value)
    );
  }
  return props.user.projects;
});

const hasProjects = computed(() => {
  return props.user.projects && props.user.projects.length > 0;
});

// ______________________________________________________________________
function updateSearch(val) {
  enteredSearchTerm.value = val;
}

// ______________________________________________________________________
watch(
  enteredSearchTerm,
  function (newVal) {
    setTimeout(() => {
      if (newVal === enteredSearchTerm.value) {
        activeSearchTerm.value = newVal;
      }
    }, 300);
  },
  { immediate: true }
);

// const propsWithRefs = toRefs(props);
// const user = propsWithRefs.user;

const { user } = toRefs(props);

//WARNING: Object(s) inside props are NOT ref(s) or reactive! ( => props.user )
// watch(props, function () {
watch(user, function () {
  enteredSearchTerm.value = '';
});

// ______________________________________________________________________
// export default {
//   components: {
//     ProjectItem,
//   },
//   props: ['user'],
//   data() {
//     return {
//       enteredSearchTerm: '',
//       activeSearchTerm: '',
//     };
//   },
//   computed: {
//     hasProjects() {
//       return this.user.projects && this.availableProjects.length > 0;
//     },
//     availableProjects() {
//       if (this.activeSearchTerm) {
//         return this.user.projects.filter((prj) =>
//           prj.title.includes(this.activeSearchTerm)
//         );
//       }
//       return this.user.projects;
//     },
//   },
//   methods: {
//     updateSearch(val) {
//       this.enteredSearchTerm = val;
//     },
//   },
//   watch: {
//     enteredSearchTerm(val) {
//       setTimeout(() => {
//         if (val === this.enteredSearchTerm) {
//           this.activeSearchTerm = val;
//         }
//       }, 300);
//     },
//     user() {
//       this.enteredSearchTerm = '';
//     },
//   },
// };
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
