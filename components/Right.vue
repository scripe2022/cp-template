<template>
    <div class="code-block">
        <highlightjs language="cpp" :code="sourceCode" />
        <div class="copy-icon" @click="copyCode">
            <font-awesome-icon :icon="['fas', 'copy']" />
        </div>
    </div>
</template>

<style>
    .hljs {
        background-color: #FFFFFF;
    }
    .code-block {
        position: relative;
    }
    .copy-icon {
        position: fixed;
        top: 10px;
        left: calc(100% - 50px);
        font-size: 30px;
    }
    .copy-icon:hover {
        cursor: pointer;
        color: gray;
    }
</style>

<script setup>
    // import 'highlight.js/styles/stackoverflow-light.css';
    import 'highlight.js/styles/github.css';
    import 'highlight.js/lib/common';
    import globalConfig from '../config/config.json';

    let sourceCode = ref(``)

    const props = defineProps({
        data: Object,
    });

    watch(() => props.data, (newValue, oldValue) => {
        build([...newValue.value]);
    }, {deep: true});

    const getCode = (url) => {
        return new Promise((resolve, reject) => {
            fetch(url)
                .then(response => response.text())
                .then(data => {
                    resolve(data);
                })
                .catch(error => {
                    reject(error);
                });
        });
    };

    const build = (f) => {
        const tree = {};
        const queue = [];
        let head = 0;
        globalConfig.forEach(element => {
            tree[element.id] = [];
            queue.push({cat: element.id, obj: element});
        });
        while (head < queue.length) {
            const node = queue[head++];
            if (node.obj.hasOwnProperty('children')) {
                node.obj.children.forEach(element => {
                    if (f.includes(node.obj.id)) f.push(element.id);
                    queue.push({cat: node.cat, obj: element});
                });
            }
            else {
                if (f.includes(node.obj.id)) {
                    tree[node.cat].push({id: node.obj.id, url: '/snippet/' + node.obj.id + '.txt'});
                }
            }
        }
        if (tree['graph'].length > 0) {
            tree['graph'].unshift({id: 'graphheader', url: '/snippet/graphheader.txt'});
            tree['graph'].push({id: 'graphfooter', url: '/snippet/graphfooter.txt'});
        }
        if (tree['network'].length > 0) {
            tree['network'].unshift({id: 'networkheader', url: '/snippet/networkheader.txt'});
            tree['network'].push({id: 'networkfooter', url: '/snippet/networkfooter.txt'});
        }
        if (tree['tree'].length > 0) {
            tree['tree'].unshift({id: 'treeheader', url: '/snippet/treeheader.txt'});
            tree['tree'].push({id: 'treefooter', url: '/snippet/treefooter.txt'});
            if (tree['tree'].includes('lca') && tree['tree'].includes('ancestor')) {
                tree['tree'].push({id: 'lcaheader', url: '/snippet/lcaheader.txt'});
                tree['tree'].push({id: 'lcafooter', url: '/snippet/lcafooter.txt'});
            }
        }
        if (tree['math'].length > 0) {
            let remained = false;
            for (let i = 0; i < tree['math'].length; ++i) {
                if (tree['math'][i].id != 'modint' && tree['math'][i].id != 'modint_dynamic') {
                    remained = true;
                    break;
                }
            }
            if (remained) {
                // tree['math'].unshift({id: 'mathheader', url: '/snippet/mathheader.txt'});
                tree['math'].push({id: 'mathfooter', url: '/snippet/mathfooter.txt'});
                let i = 0;
                while (tree['math'][i].id == 'modint' || tree['math'][i].id == 'modint_dynamic') ++i;
                tree['math'] = [
                    ...tree['math'].slice(0, i),
                    {id: 'mathheader', url: '/snippet/mathheader.txt'},
                    ...tree['math'].slice(i)
                ];
            }
        }
        if (tree['str'].length > 0) {
            const reserved = ['trie', 'acauto', 'strhash', "sam"];
            let remained = false;
            for (let i = 0; i < tree['str'].length; ++i) {
                for (let j = 0; j < reserved.length; ++j) {
                    if (!reserved.includes(tree['str'][i].id)) {
                        remained = true;
                        break;
                    }
                }
            }
            if (remained) {
                tree['str'].push({id: 'strfooter', url: '/snippet/strfooter.txt'});
                let i = 0;
                while (reserved.includes(tree['str'][i].id)) ++i;
                while (tree['str'][i].id == 'trie' || tree['str'][i].id == 'acauto') ++i;
                tree['str'] = [
                    ...tree['str'].slice(0, i),
                    {id: 'strheader', url: '/snippet/strheader.txt'},
                    ...tree['str'].slice(i)
                ];
            }
        }
        const datalist = [];
        for (let key in tree) {
            if (tree.hasOwnProperty(key)) {
                const list = tree[key];
                list.forEach(element => {
                    datalist.push(getCode(element.url));
                });
            }
        }
        let newCode = '';
        Promise.all(datalist).then((values) => {
            values.forEach(code => {
                newCode += code;
            })
            sourceCode.value = newCode;
        });
    }

    const copyCode = () => {
        navigator.clipboard.writeText(sourceCode.value);
    }

</script>

<script>
    import hljsVuePlugin from "@highlightjs/vue-plugin";
    const highlightjs = hljsVuePlugin.component;
    console.log(highlightjs);

    export default {
        components: {
            highlightjs
        }
    }
</script>