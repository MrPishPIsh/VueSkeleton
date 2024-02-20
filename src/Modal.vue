<script setup>
const props = defineProps(['hidden', 'modelValue'])
const emit = defineEmits(['submit', 'closeModal', 'update:modelValue']);
</script>

<template>
    <div id="text-input-modal" class="modal" :hidden="props.hidden">
        <div class="modal-content">
            <span class="close" @click="emit('closeModal')">&times;</span>
            <p>Add an Activity</p>
            <textarea 
                :value="props.modelValue" @input="emit('update:modelValue', $event.target.value)" 
                id="description-input" rows="10" placeholder="Add description..."
            ></textarea>
            <p>
                <button @click="emit('submit')">Submit</button>
            </p>
        </div>
    </div>
</template>