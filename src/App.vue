<template>
    <div id="app">
        <!-- <FileList></FileList> -->
        <div id="filelist">
            <div class="f-left">
                选择要修改的文件,或将文件拖拽到右下方区域
                <select name="" id="" v-model="selectedFile" @change="changeFile($event, selectedFile)">
                    <option :value="item" v-for="item in fileList" :key="item.name">{{item.name}}</option>
                </select>
            </div>
            <div class="f-right">
                <button @click="save" :disabled="currentFilePath == ''" :class="{disable: currentFilePath == ''}">保存</button>
            </div>
        </div>
        <div class="info">
            <span>当前文件路径:<span class="i-path">{{currentFilePath}}</span></span>
        </div>
        <div class="content">
            <div class="left-config">
                <ul>
                    <li v-for="item in configList" :key="item.id">{{item.name}}</li>
                </ul>
            </div>
            <div class="right-file">
                <textarea name="FileContent" ref="FileContent" id="FileContent" cols="30" rows="10" placeholder="请编辑文件内容..." v-model="test"></textarea>
            </div>
        </div>
    </div>
</template>

<script>
let fs = require('fs');
let filesList = require('../public/data.js');
let map = new Map();
let initConfig = (arr, children) => {
    let fList = arr ? arr : filesList;
    children = children ? children : 'configList';
    fList.forEach(item => {
        if (item[children]) {
            map.set(item.id, item[children]);
        }
    });
    return map;
};
let getConfigList = id => {
    if (map.has(id)) {
        return map.get(id);
    } else {
        return [];
    }
};
export default {
    name: 'app',
    data () {
        return {
            test: '',
            configList: [],
            selectedFile: {},
            fileList: '',
            currentFilePath: ''
        };
    },
    components: {
    },
    methods: {
        save () {
            fs.writeFile(this.currentFilePath, this.test, function(err) {
                if (err) throw err;
                console.log('write success');
            });
        },
        changeFile (e, selectedFile) {
            this.currentFilePath = selectedFile.abPath;
            this.configList = getConfigList(selectedFile.id);
            fs.readFile(this.currentFilePath, 'utf8', (err, data) => {
                if (err) {
                    this.test = '';
                    throw err;
                }
                this.test = data;
            });
        }
    },
    mounted () {
        initConfig ();
        this.fileList = filesList;
        this.$refs.FileContent.ondragenter = this.$refs.FileContent.ondragover = this.$refs.FileContent.ondragleave = () => {
            return false; //阻止默认行为
        };
        this.$refs.FileContent.ondrop = e => {
            e.preventDefault();
            let filePath = e.dataTransfer.files[0].path;
            if (!filePath.includes('.') || /\.(json|txt|html|bat|sh)$/.test(filePath)) {
                this.$refs.FileContent.innerHTML = '';
                this.currentFilePath = filePath;
                fs.readFile(filePath, 'utf8', (err, data) => {
                    if (err) throw err;
                    this.test = data;
                });
            } else {
                console.log('null');
            }
        };
    }
};
</script>

<style lang="less">
@import url('./assets/css/reset.css');
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    display: flex;
    flex-direction: column;
    height: 100%;
    .info {
        padding: 2px 10px;
        border-bottom: 1px solid #bbb;
        .i-path {
            color: skyblue;
        }
    }
    .content {
        flex: auto;
        display: flex;
        flex-direction: row;
        .left-config {
            min-width: 200px;
        }
        .right-file {
            flex: auto;
            textarea {
                width: 100%;
            }
        }
    }
}
#filelist {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 2px 10px;
    .f-right {
        button {
            margin-left: 20px;
            padding: 2px 10px;
            border-radius: 4px;
            background-color: #67c23a;
            border: none;
            color: #fff;
            &.disable {
                background-color: #b3e19d;
            } 
        }
    }
}
#FileContent {
    resize:none;
    display: block;
    padding-right: 10px;
    width: 100%;
    height: 100%;
}
</style>
