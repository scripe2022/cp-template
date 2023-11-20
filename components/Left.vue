<template>
    <ClientOnly>
        <div class="fixed-content unselectable">
            <treeselect v-model="selected" :multiple="true" :options="options" alwaysOpen="true" defaultExpandLevel="Infinity" max-height="3000" :searchable="false">
            </treeselect>
        </div>
    </ClientOnly>
</template>

<style>
    .fixed-content {
        box-sizing: border-box;
        position: fixed;
        overflow-x: hidden;
        overflow-y: scroll;
        height: 100%;
        width: 30%;
    }
    .unselectable {
        user-select: none;
        -moz-user-select: none;
        -khtml-user-select: none;
        -webkit-user-select: none;
        -o-user-select: none;
    }
    .fixed-content > .vue-treeselect > .vue-treeselect__control {
        border: 0;
    }
    div.vue-treeselect__menu {
        border: 0;
        border-top: 1px solid #cfcfcf;
        border-bottom-left-radius: 0;
        border-bottom-right-radius: 0;
        box-shadow: 0;
    }
    .vue-treeselect__checkbox {
        border-radius: 0;
    }
</style>

<script setup>
    import Treeselect from 'vue3-treeselect'
    import 'vue3-treeselect/dist/vue3-treeselect.css'
    // import prerequisite from '../config/prerequisite.json'
    import options from '../config/config.json'

    const emit = defineEmits([
        'change',
    ])
    let selected = ref([]);
    watch(() => selected, (newValue, oldValue) => {
        // for (let key in prerequisite) {
        //     if (!newValue.value.includes(key)) continue;

        //     console.log(key)
        // }
        // console.log(selected.value);
        // console.log(selected.value.includes('ancestor'))
        // selected.value.push("ancestor");
        emit('change', newValue);
    }, {deep: true});

</script>