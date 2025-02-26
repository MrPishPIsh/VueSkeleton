<script setup>
import { ref, watch } from 'vue';
import Modal from './Modal.vue';

class Activity {
  description = "";
  isCompleted = false;
  
  constructor(description) {
    this.description = description;
  }

  static toggleCompleted(activity) {
    activity.isCompleted = !activity.isCompleted;
  }
}

class ActivityRegistry extends Map {
  /**
   * Gets the largest index in the registry.
   */
  getLargestIndex() {
    // if no indices are found, then the registry is empty
    let largestIndex = -1;

    for (let [index] of this) {
      index = Number(index)
      if (index > largestIndex) {
        largestIndex = index;
        continue;
      }
    }
    
    return largestIndex;
  }

  toJson() {
    let entryArray = Array.from(this.entries());
    return JSON.stringify(entryArray);
  }

  /**
   * Creates and adds a new activity to the registry with a given description.
   * @param {String} description 
   */
  add(description) {
    if (description === '' || description === undefined || description === null) 
      return;

    let index = this.getLargestIndex() + 1;
    this.set(index, new Activity(description));
  }
}


const isModalHidden = ref(true);
function closeModal() {
  isModalHidden.value = true;
  description.value = '';
}

const description = ref('');

const registryStorageName = "registries";
let registry = ref(new ActivityRegistry());
function addNewRegistry() {
  registry.value.add(description.value)
  description.value = '';
}

const registryJson = localStorage.getItem(registryStorageName);
if (registryJson !== null) {
  let jsonStream = JSON.parse(registryJson);
  registry = ref(new ActivityRegistry(jsonStream))
}

watch(registry, (newRegistry) => {
  localStorage.setItem(registryStorageName, newRegistry.toJson());
}, { deep: true });
</script>

<template>
  <h1 style="text-align: center;">Todo App</h1>

  <Modal :hidden="isModalHidden" v-model="description" @submit="addNewRegistry()" @closeModal="closeModal()"></Modal>
  
  <div>
    <button @click="isModalHidden = false">Add Activity</button>
  </div>
  
  <div>
    <table id="activity-registry">
      <tr>
        <th>ID</th>
        <th>Description</th>
        <th>Completed</th>
        <th></th>
      </tr>
      <tr v-for="[key, item] in registry">
        <td>{{ key }}</td>
        <td>{{ item.description }}</td>
        <td>
          <button 
            :style="{ color: 'white', background: item.isCompleted ? 'green' : 'red' }" 
            @click="Activity.toggleCompleted(item)">{{ item.isCompleted }}
          </button>
        </td>
        <td>
          <button @click="registry.delete(key)">Remove</button>
        </td>
      </tr>
    </table>
  </div>
</template>
