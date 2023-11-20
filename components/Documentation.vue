<template>
    <div class="documentation">
        <div class="left-col">
            <Left @change="update"></Left>
        </div>
        <div class="right-col">
            <Right :data="value"></Right>
        </div>
    </div>
</template>

<style>
    html {
        -ms-overflow-style: none;  /* Internet Explorer 10+ */
        scrollbar-width: none;  /* Firefox */
    }
    html::-webkit-scrollbar { 
        display: none;  /* Safari and Chrome */
    }
    body, pre {
        margin: 0;
        padding: 0;
        font-family: 'Fira Code', monospace;
    }
    .documentation {
        display: flex;
        flex-direction: row;
        width: 100%;
    }
    .documentation > .left-col {
        width: 30%;
    }
    .documentation > .right-col {
        width: 70%;
    }
</style>

<script setup>
    let value = ref([]);
    const update = (selected) => {
        value.value = selected;
    }
</script>