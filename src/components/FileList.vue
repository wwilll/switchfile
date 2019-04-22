<template>
    <div id="filelist">
        <select name="" id="" v-model="selectedFile" @change="changeFile($event, selectedFile)">
            <option :value="item.abPath" v-for="item in fileList" :key="item.id">{{item.name}}</option>
        </select>
        <span>当前文件路径：{{currentFile}}</span>
    </div>
</template>

<script>
export default {
    name: 'FileList',
    data() {
        return {
            selectedFile: '',
            fileList: '',
            currentFile: ''
        }
    },
    computed: {
        getCurrentFile() {
            return 11
        }
    },
    mounted() {
        this.fileList = [
            {id: '1', name: 'AAAA', abPath: 'C://f/a.txt', rePath: '../s.txt'},
            {id: '2', name: 'B', abPath: 'C://f/b.txt', rePath: '../s.txt'},
            {id: '3', name: 'C', abPath: 'C://f/c.txt', rePath: '../s.txt'}
        ]
    },
    methods: {
        changeFile(e, abPath) {
            console.log(e, abPath)
            this.currentFile = abPath;
        }
    }
}
</script>

