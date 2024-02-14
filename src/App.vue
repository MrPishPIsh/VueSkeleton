<script setup>
import { ref, onMounted, watch, toHandlerKey } from 'vue'

const msg = 'Todo App';

function createTableCell(data) {
  let cell = document.createElement("td");
  if (typeof data === 'number' || typeof data === 'string') {
    cell.innerText = data;
    return cell;
  }

  cell.appendChild(data);
  return cell;
}

class Activity {
  description = "";
  isCompleted = false;
  
  constructor(description) {
    this.description = description;
  }

  toggledCompleted(element) {
    this.isCompleted = !this.isCompleted;
    element.innerText = this.isCompleted;
    element.style.background = this.isCompleted ? 'green' : 'red';
  }

  toTableRow(index) {
    let row = document.createElement("tr");
    row.appendChild(createTableCell(index));
    row.appendChild(createTableCell(this.description));
    
    let button = document.createElement("button");
    button.innerText = this.isCompleted;
    button.style.background = 'red';
    button.onclick = () => this.toggledCompleted(button);
    row.appendChild(createTableCell(button)); 

    return row;
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

  /**
   * Creates and adds a new activity to the registry with a given description.
   * @param {String} description 
   */
  add() {
    let index = this.getLargestIndex() + 1;
    let description = prompt("Input activity description: ");
    let activity = new Activity(description);
    this.set(index, activity);

    let row = activity.toTableRow(index);
    let deleteButton = document.createElement("button");
    deleteButton.innerText = "Remove";
    deleteButton.onclick = () => {
      this.delete(index);
      row.remove();
    };

    row.appendChild(createTableCell(deleteButton));

    let table = document.getElementById("activity-registry");
    table.appendChild(row);
  }
}

const registry = ref(new ActivityRegistry());

</script>

<template>
  <h1 style="text-align: center;">
    {{ msg }}
  </h1>

  <div>
    <button type="button" @click="registry.add()">Add Activity</button>
  </div>

  <div>
    <table id="activity-registry">
      <tr>
        <th>ID</th>
        <th>Description</th>
        <th>Completed</th>
        <th></th>
      </tr>
    </table>
  </div>
</template>
