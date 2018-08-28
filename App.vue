<template>
    <div id="app" style="padding-bottom: 300px;">
        <img src="assets/logo.png">
        <h1>{{ msg }}</h1>

        <h2>Extended TreeView</h2>
        <div>
            <div style="width:840px; margin: 0 auto;">
                <div style="width:49%; display:inline-block; vertical-align: top;">
                    <v-jstree :data="data"
                              show-checkbox
                              multiple
                              allow-batch
                              whole-row
                              children-counter
                              ref="treeMock"></v-jstree>
                </div></div>

        </div>

        <br/>
        <div>This is extended version of <a href="https://github.com/zdy1988/vue-jstree">vue-jstree</a>.
            Standard usage is presented under: <a href="https://zdy1988.github.io/vue-jstree/">vue-jstree by zdy1988</a></div>
    </div>
</template>

<script>
    export default {
        name: 'app',
        data () {
            return {
                msg: 'A Tree Plugin For Vue2',
                searchText: '',
                editingItem: {},
                editingNode: null,
                itemEvents: {
                    mouseover: function () {
                        console.log('mouseover')
                    },
                    contextmenu: function () {
                        console.log(arguments[2])
                        arguments[2].preventDefault()
                        console.log('contextmenu')
                    }
                },
                data: [
                    {
                        text: 'A',
                        selected: 'child',
                        opened: true,
                        children: [
                            {
                                text: 'AA',
                                selected: 'no',
                                opened: true,
                                children: [
                                    {
                                        text: 'AAA',
                                        selected: 'no',
                                        opened: true,
                                        children: [
                                            {text: 'Set 0', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 1', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 2', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 3', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 4', selected: 'no', icon: 'fa fa-file-o'}
                                        ]
                                    },
                                    {
                                        text: 'AAB',
                                        selected: 'no',
                                        opened: true,
                                        children: [
                                            {text: 'Set 5', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 6', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 7', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 8', selected: 'no', icon: 'fa fa-file-o'}
                                        ]
                                    }
                                ]
                            },
                            {text: 'AB', selected: 'yes'},
                            {text: 'AC', selected: 'yes'}
                        ]
                    },
                    {
                        text: 'B',
                        selected: 'child',
                        opened: true,
                        children: [
                            {
                                text: 'BA',
                                selected: 'child',
                                opened: true,
                                children: [
                                    {
                                        text: 'BAA',
                                        selected: 'child',
                                        opened: true,
                                        children: [
                                            {text: 'Set 9', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 10', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 11', selected: 'no', icon: 'fa fa-file-o'},
                                            {text: 'Set 12', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 13', selected: 'yes', icon: 'fa fa-file-o'}
                                        ]
                                    },
                                    {
                                        text: 'BAB',
                                        selected: 'yes',
                                        opened: true,
                                        children: [
                                            {text: 'Set 14', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 15', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 16', selected: 'yes', icon: 'fa fa-file-o'},
                                            {text: 'Set 17', selected: 'yes', icon: 'fa fa-file-o'}
                                        ]
                                    }
                                ]
                            }
                        ]
                    }
                ],
                asyncData: [],
                loadData: function (oriNode, resolve) {
                    var id = oriNode.data.id ? oriNode.data.id : 0
                    setTimeout(() => {
                        let data = []
                        if (id > 200) {
                            data = []
                        }
                        else {
                            data = [
                                {
                                    "text": "New Item 1..." + id, "isLeaf": id > 100
                                },
                                {
                                    "text": "New Item 2..." + id, "isLeaf": id > 100
                                }
                            ]
                        }
                        resolve(data)
                    }, 500)
                },
                data3: [
                    {"text": "root"}
                ],
                filesToAdd: 1,
                filesToAddIndex: 0
            }
        },
        methods: {
            itemClick (node) {
                this.editingNode = node
                this.editingItem = node.model
                console.log(node.model.text + ' clicked !')
            },
            itemDragStart (node) {
                console.log(node.model.text + ' drag start !')
            },
            itemDragEnd (node) {
                console.log(node.model.text + ' drag end !')
            },
            itemDropBefore (node, item, draggedItem , e) {
                if (!draggedItem) {
                    item.addChild({
                        text: "newNode",
                        value: "newNode"
                    })
                }
            },
            itemDrop (node, item) {
                var sortBy = function(attr,rev) {
                    if (rev == undefined) {
                        rev = 1;
                    } else {
                        rev = (rev) ? 1 : -1;
                    }
                    return function (a, b) {
                        a = a[attr];
                        b = b[attr];
                        if (a < b) {
                            return rev * -1;
                        }
                        if (a > b) {
                            return rev * 1;
                        }
                        return 0;
                    }
                }
                item.children.sort(sortBy('text', true))
                console.log(node.model.text + ' drop !')
            },
            inputKeyUp: function () {
                var text = this.searchText
                const patt = new RegExp(text);
                this.$refs.tree.handleRecursionNodeChilds(this.$refs.tree, function (node) {
                    if (text !== '' && node.model !== undefined) {
                        const str = node.model.text
                        if (patt.test(str)) {
                            node.$el.querySelector('.tree-anchor').style.color = 'red'
                        } else {
                            node.$el.querySelector('.tree-anchor').style.color = '#000'
                        } // or other operations
                    } else {
                        node.$el.querySelector('.tree-anchor').style.color = '#000'
                    }
                })
            },
            addChildNode: function () {
                if (this.editingItem.id !== undefined) {
                    this.editingItem.addChild({
                        text: "newNode"
                    })
                }
            },
            removeNode: function () {
                if (this.editingItem.id !== undefined) {
                    var index = this.editingNode.parentItem.indexOf(this.editingItem)
                    this.editingNode.parentItem.splice(index, 1)
                }
            },
            addBeforeNode: function () {
                if (this.editingItem.id !== undefined) {
                    this.editingItem.addBefore({
                        text: this.editingItem.text + " before"
                    }, this.editingNode)
                }
            },
            addAfterNode: function () {
                if (this.editingItem.id !== undefined) {
                    this.editingItem.addAfter({
                        text: this.editingItem.text + " after"
                    }, this.editingNode)
                }
            },
            openChildren: function () {
                if (this.editingItem.id !== undefined) {
                    this.editingItem.openChildren()
                }
            },
            closeChildren: function () {
                if (this.editingItem.id !== undefined) {
                    this.editingItem.closeChildren()
                }
            },
            refreshNode: function () {
                this.asyncData = [
                    this.$refs.tree2.initializeLoading()
                ]
                this.$refs.tree2.handleAsyncLoad(this.asyncData, this.$refs.tree2)
            },
            customItemClick: function (node ,item, e) {
                e.stopPropagation()
                var index = node.parentItem.indexOf(item)
                node.parentItem.splice(index, 1)
            },
            customItemClickWithCtrl: function () {
                console.log('click + ctrl')
            },
            fillData: function () {
                if (this.editingItem.id !== undefined) {
                    for (var i = 0; i < this.filesToAdd; i++) {
                        this.filesToAddIndex++
                        this.editingItem.addChild({
                            text: "File" + this.filesToAddIndex,
                            icon: "fa fa-file icon-state-danger"
                        })
                    }
                }
            }
        }
    }
</script>

<style>
    #app {
        font-family: 'Avenir', Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

    h1, h2 {
        font-weight: normal;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }

    div{
        display: block;
    }

    table {
        width: 100%;
        border-collapse: collapse;
        border: 1px solid #EEE;
        font-size: 14px;
    }

    table th {
        background: #EEE;
        border-bottom: 1px solid #CCC;
        padding: 4px;
    }

    table td {
        border: 1px solid #EEE;
        padding: 4px;
    }
    .icon-state-default {
        color: gray;
    }
    .icon-state-danger {
        color: red;
    }
    .icon-state-warning {
        color: yellow;
    }
    .icon-state-success {
        color: green;
    }
</style>
