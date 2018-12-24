<template>
    <div class="transfer2" :style="{width,height}">
        <template>
            <!-- 左侧穿梭框 原料框 -->
            <div class="transfer-left">
                <h3 class="transfer-title">
                   <p v-for="(item, index) in title[0]" :class="{'active':Index== index }" @click="FirstClick(index)">
                       <span>{{item.title}}</span>
                       <span class="length">{{index == 0 ? checkedFromDataLength + '/' + formDataLength.one : TwoCheckedFromDataLength + '/' +formDataLength.two}}</span>
                   </p>
                </h3>
                <!-- 内容区 -->
                <div class="transfer-main">
                    <el-tree ref='from-tree' :data="self_from_data" show-checkbox :node-key="node_key"
                             @check='fromTreeChecked' :props="defaultProps"
                             :default-expand-all="openAll" :default-checked-keys="defaultCheckedKeys"/>
                </div>
            </div>
            <!-- 穿梭区 按钮框 -->
            <div class="transfer-center">
                <template>
                    <p class="transfer-center-item">
                        <el-button type="primary" @click="addToAims" icon="el-icon-arrow-right" circle
                                   :disabled="from_disabled"></el-button>
                    </p>
                    <p class="transfer-center-item">
                        <el-button type="primary" @click='removeToSource' :disabled="to_disabled"
                                   icon="el-icon-arrow-left" circle></el-button>
                    </p>
                </template>
            </div>
            <!-- 右侧穿梭框 目标框 -->
            <div class="transfer-right">
                <h3 class="transfer-title">
                    <p v-for="(item, index) in title[0]" :class="{'active':Index== index }" @click="FirstClick(index)">
                        <span>{{item.title}}</span>
                        <span class="length">{{index == 0 ? checkedToDataLength + '/' + toDataLength.one : TwoCheckedToDataLength + '/' +toDataLength.two}}</span>
                    </p>
                </h3>
                <!-- 内容区 -->
                <div class="transfer-main">
                    <!--<el-input v-if="filter" :placeholder="placeholder" v-model="filterTo" size="small" class="filter-tree">-->
                    <!--</el-input>-->
                    <el-tree slot='to'
                             ref='to-tree'
                             :data="self_to_data"
                             show-checkbox
                             :node-key="node_key"
                             @check='toTreeChecked'
                             :default-expanded-keys="to_expanded_keys"
                             :props="defaultProps"
                             :filter-node-method="filterNodeTo"
                             :default-expand-all="openAll"/>
                </div>
            </div>
        </template>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                from_is_indeterminate: false, // 源数据是否半选
                from_check_all: false, // 源数据是否全选
                to_is_indeterminate: false, // 目标数据是否半选
                to_check_all: false, // 目标数据是否全选
                from_expanded_keys: [], // 源数据展开节点
                to_expanded_keys: [], // 目标数据展开节点
                self_from: [], // 子组件左侧数据
                self_to: [], // 子组件右侧数据
                from_disabled: true, // 添加按钮是否禁用
                to_disabled: true, // 移除按钮是否禁用
                from_check_keys: [], // 源数据选中key数组 以此属性关联穿梭按钮，总全选、半选状态
                to_check_keys: [], // 目标数据选中key数组 以此属性关联穿梭按钮，总全选、半选状态
                filterFrom: "", // 源数据筛选
                filterTo: "", // 目标数据筛选
                /*******新增参数获取选中的长度**********/
                checkedFromDataLength: 0,
                checkedToDataLength: 0,
                TwoCheckedFromDataLength: 0,
                TwoCheckedToDataLength:0,
                /**************************/
                Index: 0
            };
        },
        props: {
            // 宽度
            width: {
                type: String,
                default: "100%"
            },
            list: {
                type:Object,
                default: null
            },
            // 高度
            height: {
                type: String,
                default: "320px"
            },
            // 标题
            title: {
                type: Array,
                default: () => ["源列表", "目标列表"]
            },
            // 穿梭按钮名字
            button_text: Array,
            // 源数据
            // from_data: {
            //     type: Array,
            //     default: () => []
            // },
            // // 选中数据
            // to_data: {
            //     type: Array,
            //     default: () => []
            // },
            // el-tree 配置项
            defaultProps: {
                type: Object,
                default: () => {
                    return {label: "label", children: "children"};
                }
            },
            // el-tree node-key 必须唯一
            node_key: {
                type: String,
                default: "id"
            },
            // 自定义 pid参数名
            pid: {
                type: String,
                default: "pid"
            },
            // 是否只返回叶子节点
            leafOnly: {
                type: Boolean,
                default: false
            },
            // 是否启用筛选
            filter: {
                type: Boolean,
                default: false
            },
            // 是否展开所有节点
            openAll: {
                type: Boolean,
                default: false
            },
            // 自定义树节点
            renderContent: Function,
            // 穿梭后是否展开节点
            transferOpenNode: {
                type: Boolean,
                default: true
            },
            // 源数据 默认选中节点
            defaultCheckedKeys: {
                type: Array,
                default: () => []
            },
            // 筛选placeholder
            placeholder: {
                type: String,
                default: "输入关键字进行过滤"
            },
            // 默认穿梭一次默认选中数据
            defaultTransfer: {
                type: Boolean,
                default: false
            },
        },
        methods: {

            //切换class
            FirstClick(index) {
                this.Index =index;
                this.self_from = []
                this.self_to = []
                // if(index == 0) {
                //     this.self_from = this.list.one.fromData
                //     this.self_to = this.list.one.toData
                // } else {
                //     this.self_from = this.list.two.fromData
                //     this.self_to = this.list.two.toData
                // }
            },

            // 添加按钮
            addToAims() {
                // 获取选中通过穿梭框的keys - 仅用于传送纯净的id数组到父组件同后台通信
                let keys = this.$refs["from-tree"].getCheckedKeys(this.leafOnly);
                // 获取半选通过穿梭框的keys - 仅用于传送纯净的id数组到父组件同后台通信
                let harfKeys = this.$refs["from-tree"].getHalfCheckedKeys();
                // 选中节点数据
                let arrayCheckedNodes = this.$refs["from-tree"].getCheckedNodes(
                    this.leafOnly
                );
                // 获取选中通过穿梭框的nodes - 仅用于传送选中节点数组到父组件同后台通信需求
                let nodes = JSON.parse(JSON.stringify(arrayCheckedNodes));
                // 半选中节点数据
                let arrayHalfCheckedNodes = this.$refs["from-tree"].getHalfCheckedNodes();
                // 获取半选通过穿梭框的nodes - 仅用于传送选中节点数组到父组件同后台通信需求
                let halfNodes = JSON.parse(JSON.stringify(arrayHalfCheckedNodes));
                // 自定义参数读取设置
                let children__ = this.defaultProps.children || "children";
                let pid__ = this.pid || "pid";
                let id__ = this["node_key"] || "id";
                /*
                 * 先整合目标树没有父节点的叶子节点选中，需要整理出来此叶子节点的父节点直到根节点路径 - 此时所有骨架节点已有
                 * 再将所有末端叶子节点根据pid直接推入目标树即可
                 * 声明新盒子将所有半选节点的子节点清除 - 只保留骨架 因为排序是先父后子 因此不存在子元素处理好插入时父元素还没处理的情况
                 * 下面一二步是为了搭建出来目标树没有根节点躯干节点时的叶子选中，给此叶子搭建出根节点和躯干节点
                 */
                // let不存在状态提升 因此在函数调用之前赋值 并递归为以为数组！
                let self_to_data = JSON.stringify(this.self_to_data);
                // 第一步
                let skeletonHalfCheckedNodes = JSON.parse(
                    JSON.stringify(arrayHalfCheckedNodes)
                ); // 深拷贝数据 - 半选节点
                // 筛选目标树不存在的骨架节点 - 半选内的节点
                let newSkeletonHalfCheckedNodes = [];
                skeletonHalfCheckedNodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        newSkeletonHalfCheckedNodes.push(item);
                    }
                });
                // 筛选到目标树不存在的骨架后在处理每个骨架节点-非末端叶子节点 - 半选节点
                newSkeletonHalfCheckedNodes.forEach(item => {
                    item[children__] = [];
                    if (item[pid__] == 0) {
                        this.$refs["to-tree"].append(item);
                    } else {
                        this.$refs["to-tree"].append(item, item[pid__]);
                    }
                });
                // 第二步
                /*  let cloneSkeletonCheckedNodes = JSON.parse(
                  JSON.stringify(arrayCheckedNodes)
                ); // 深拷贝数据 -选中节点 */
                // 筛选目标树不存在的骨架节点 - 全选内的节点
                let newSkeletonCheckedNodes = [];
                nodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        newSkeletonCheckedNodes.push(item);
                    }
                });
                // 筛选到目标树不存在的骨架后在处理每个骨架节点-非末端叶子节点 - 全选节点
                newSkeletonCheckedNodes.forEach(item => {
                    if (item[children__] && item[children__].length > 0) {
                        item[children__] = [];
                        this.$refs["to-tree"].append(item, item[pid__]);
                    }
                });
                // 第三步 处理末端叶子元素 - 声明新盒子筛选出所有末端叶子节点
                let leafCheckedNodes = arrayCheckedNodes.filter(
                    item => !item[children__] || item[children__].length == 0
                );
                // 末端叶子插入目标树
                leafCheckedNodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        this.$refs["to-tree"].append(item, item[pid__]);
                    }
                });

                // 递归查询data内是否存在item函数
                function inquireIsExist(item, strData = self_to_data) {
                    // 将树形数据格式化成一维字符串 然后通过匹配来判断是否已存在
                    let strItem =
                        typeof item[id__] == "number"
                            ? `"${id__}":${item[id__]},`
                            : `"${id__}":"${item[id__]}"`;
                    let reg = RegExp(strItem);
                    let existed = reg.test(strData);
                    return existed;
                }

                // 左侧删掉选中数据
                arrayCheckedNodes.forEach(item => {
                    this.$refs["from-tree"].remove(item);
                });
                // 处理完毕按钮恢复禁用状态
                this.from_check_keys = [];
                // 目标数据节点展开
                if (this.transferOpenNode) {
                    this.to_expanded_keys = keys;
                }

                this.Index ? this.TwoCheckedFromDataLength = 0 : this.checkedFromDataLength = 0
                this.$refs['from-tree'].setCheckedNodes([])

                // 传递信息给父组件
                this.$emit("addBtn", this.self_from_data, this.self_to_data, {
                    keys,
                    nodes,
                    harfKeys,
                    halfNodes
                }, this.Index);
            },
            // 移除按钮
            removeToSource() {
                // 获取选中通过穿梭框的keys - 仅用于传送纯净的id数组到父组件同后台通信
                let keys = this.$refs["to-tree"].getCheckedKeys(this.leafOnly);
                // 获取半选通过穿梭框的keys - 仅用于传送纯净的id数组到父组件同后台通信
                let harfKeys = this.$refs["to-tree"].getHalfCheckedKeys();
                // 获取选中通过穿梭框的nodes 选中节点数据
                let arrayCheckedNodes = this.$refs["to-tree"].getCheckedNodes();
                // 获取选中通过穿梭框的nodes - 仅用于传送选中节点数组到父组件同后台通信需求
                let nodes = JSON.parse(JSON.stringify(arrayCheckedNodes));
                // 半选中节点数据
                let arrayHalfCheckedNodes = this.$refs["to-tree"].getHalfCheckedNodes();
                // 获取半选通过穿梭框的nodes - 仅用于传送选中节点数组到父组件同后台通信需求
                let halfNodes = JSON.parse(JSON.stringify(arrayHalfCheckedNodes));
                // 自定义参数读取设置
                let children__ = this.defaultProps.children || "children";
                let pid__ = this.pid || "pid";
                let id__ = this["node_key"] || "id";
                let self_from_data = JSON.stringify(this.self_from_data);
                // 第一步
                let skeletonHalfCheckedNodes = JSON.parse(
                    JSON.stringify(arrayHalfCheckedNodes)
                ); // 深拷贝数据 - 半选节点
                // 筛选目标树不存在的骨架节点 - 半选内的节点
                let newSkeletonHalfCheckedNodes = [];
                skeletonHalfCheckedNodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        newSkeletonHalfCheckedNodes.push(item);
                    }
                });
                // 筛选到目标树不存在的骨架后在处理每个骨架节点-非末端叶子节点 - 半选节点
                newSkeletonHalfCheckedNodes.forEach(item => {
                    item[children__] = [];
                    if (item[pid__] == 0) {
                        this.$refs["from-tree"].append(item);
                    } else {
                        this.$refs["from-tree"].append(item, item[pid__]);
                    }
                }); // 深拷贝数据 -选中节点 // 筛选目标树不存在的骨架节点 - 全选内的节点
                // 第二步
                let newSkeletonCheckedNodes = [];
                nodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        newSkeletonCheckedNodes.push(item);
                    }
                });
                // 筛选到目标树不存在的骨架后在处理每个骨架节点-非末端叶子节点 - 全选节点
                newSkeletonCheckedNodes.forEach(item => {
                    if (item[children__] && item[children__].length > 0) {
                        item[children__] = [];
                        this.$refs["from-tree"].append(item, item[pid__]);
                    }
                });
                // 第三步 处理末端叶子元素 - 声明新盒子筛选出所有末端叶子节点
                let leafCheckedNodes = arrayCheckedNodes.filter(
                    item => !item[children__] || item[children__].length == 0
                );
                // 末端叶子直接插入目标树
                leafCheckedNodes.forEach(item => {
                    if (!inquireIsExist(item)) {
                        this.$refs["from-tree"].append(item, item[pid__]);
                    }
                });

                // 递归查询data内是否存在item函数
                function inquireIsExist(item, strData = self_from_data) {
                    // 将树形数据格式化成一维字符串 然后通过匹配来判断是否已存在
                    let strItem =
                        typeof item[id__] == "number"
                            ? `"${id__}":${item[id__]},`
                            : `"${id__}":"${item[id__]}"`;
                    let reg = RegExp(strItem);
                    let existed = reg.test(strData);
                    return existed;
                }

                // 右侧删掉选中数据
                arrayCheckedNodes.forEach(item => {
                    this.$refs["to-tree"].remove(item);
                });
                // 处理完毕按钮恢复禁用状态
                this.to_check_keys = [];
                // 目标数据节点展开
                if (this.transferOpenNode) {
                    this.from_expanded_keys = keys;
                }

                this.Index ? this.TwoCheckedToDataLength = 0 : this.checkedToDataLength = 0
                this.$refs['to-tree'].setCheckedNodes([])
                // 传递信息给父组件
                this.$emit("removeBtn", this.self_from_data, this.self_to_data, {
                    keys,
                    nodes,
                    harfKeys,
                    halfNodes
                }, this.Index);
            },
            // 源树选中事件 - 是否禁用穿梭按钮
            fromTreeChecked(nodeObj, treeObj) {
                this.Index ? this.TwoCheckedFromDataLength = 0 : this.checkedFromDataLength = 0
                this.from_check_keys = treeObj.checkedNodes;
                for (let i = 0; i < treeObj.checkedNodes.length; i++) {
                    let off = this.Index ? !treeObj.checkedNodes[i].children : !treeObj.checkedNodes[i].children.length

                    if (treeObj.checkedNodes[i].children == 'undefined'|| off) {
                        this.Index == 0 ? this.checkedFromDataLength++ : this.TwoCheckedFromDataLength++;
                    }
                }
            },
            // 目标树选中事件 - 是否禁用穿梭按钮
            toTreeChecked(nodeObj, treeObj) {
                this.Index ? this.TwoCheckedToDataLength = 0 : this.checkedToDataLength = 0
                this.to_check_keys = treeObj.checkedNodes;
                for (let i = 0; i < treeObj.checkedNodes.length; i++) {
                    let off = this.Index ? !treeObj.checkedNodes[i].children : !treeObj.checkedNodes[i].children.length

                    if (treeObj.checkedNodes[i].children == 'undefined'|| off ) {
                        this.Index == 0 ? this.checkedToDataLength++ : this.TwoCheckedToDataLength++;
                    }
                }
            },
            // 源数据 总全选checkbox
            fromAllBoxChange(val) {
                this.checkedFromDataLength = this.formDataLength
                if (this.self_from_data.length == 0) {
                    return;
                }
                if (val) {
                    // let array = [...this.from_data];
                    this.from_check_keys = this.self_from_data;
                    this.$refs["from-tree"].setCheckedNodes(this.self_from_data);
                } else {
                    this.$refs["from-tree"].setCheckedNodes([]);
                    this.from_check_keys = [];
                }
            },
            // 目标数据 总全选checkbox
            toAllBoxChange(val) {
                this.checkedToDataLength = this.toDataLength
                if (this.self_to_data.length == 0) {
                    return;
                }
                if (val) {
                    this.to_check_keys = this.self_to_data;
                    this.$refs["to-tree"].setCheckedNodes(this.self_to_data);
                } else {
                    this.$refs["to-tree"].setCheckedNodes([]);
                    this.to_check_keys = [];
                }
            },
            // 源数据 筛选
            filterNodeFrom(value, data) {
                if (!value) return true;
                return data[this.defaultProps.label].indexOf(value) !== -1;
            },
            // 目标数据筛选
            filterNodeTo(value, data) {
                if (!value) return true;
                return data[this.defaultProps.label].indexOf(value) !== -1;
            },
        },
        computed: {
            /*******新增参数获取默认数组的长度**********/
            toDataLength() {
                function getLeafCountTree(json) {
                    if (json) {
                        if (!json.children ||json.children.length == 0) {
                            json.colspan = 1;
                            return 1;
                        } else {
                            var leafCount = 0;
                            for (var i = 0; i < json.children.length; i++) {
                                leafCount = leafCount + getLeafCountTree(json.children[i]);
                            }
                            json.colspan = leafCount;
                            return leafCount;
                        }
                    }
                }

                return {
                    one: getLeafCountTree(this.list.one.toData[0]) || 0,
                    two: getLeafCountTree(this.list.two.toData[0]) || 0
                };
            },
            formDataLength() {
                function getLeafCountTree(json) {
                    if (json) {
                        if ( !json.children || json.children.length == 0) {
                            json.colspan = 1;
                            return 1;
                        } else {
                            var leafCount = 0;
                            for (var i = 0; i < json.children.length; i++) {
                                leafCount = leafCount + getLeafCountTree(json.children[i]);
                            }
                            json.colspan = leafCount;
                            return leafCount;
                        }
                    }
                }

                return {
                    one: getLeafCountTree(this.list.one.fromData[0]) || 0,
                    two: getLeafCountTree(this.list.two.fromData[0]) || 0
                };
            },
            // 左侧数据
            self_from_data() {
                let data = this.Index== 0 ? this.list.one.fromData : this.list.two.fromData
                let from_array = [ ...data, ...this.self_from];
                from_array.forEach(item => {
                    item[this.pid] = 0;
                });
                return from_array;
            },
            // 右侧数据
            self_to_data() {
                let data = this.Index== 0 ? this.list.one.toData : this.list.two.toData
                let to_array = [...data, ...this.self_to];
                to_array.forEach(item => {
                    item[this.pid] = 0;
                });
                console.log(to_array, 'toArray')
                return to_array;
            },
            // 左侧菜单名
            fromTitle() {
                let [text] = this.title;
                return text;
            },
            // 右侧菜单名
            toTitle() {
                let [, text] = this.title;
                return text;
            },
            // 右侧菜单名2
            toTitleSecond() {
                let [, , text] = this.title;
                return text;
            },
            // 右侧菜单名3
            toTitleThird() {
                let [, , , text] = this.title;
                return text;
            },
            // 上部按钮名
            fromButton() {
                if (this.button_text == undefined) {
                    return;
                }
                let [text] = this.button_text;
                return text;
            },
            // 下部按钮名
            toButton() {
                if (this.button_text == undefined) {
                    return;
                }
                let [, text] = this.button_text;
                return text;
            }
        },
        watch: {
            // 左侧 状态监测
            from_check_keys(val) {
                if (val.length > 0) {
                    // 穿梭按钮是否禁用
                    this.from_disabled = false;
                    // 总半选是否开启
                    this.from_is_indeterminate = true;
                    // 总全选是否开启 - 根据选中节点中为根节点的数量是否和源数据长度相等
                    let allCheck = val.filter(item => item[this.pid] == 0);
                    if (allCheck.length == this.self_from_data.length) {
                        // 关闭半选 开启全选
                        this.from_is_indeterminate = false;
                        this.from_check_all = true;
                    } else {
                        this.from_is_indeterminate = true;
                        this.from_check_all = false;
                    }
                } else {
                    this.from_disabled = true;
                    this.from_is_indeterminate = false;
                    this.from_check_all = false;
                }
            },
            // 右侧 状态监测
            to_check_keys(val) {
                if (val.length > 0) {
                    // 穿梭按钮是否禁用
                    this.to_disabled = false;
                    // 总半选是否开启
                    this.to_is_indeterminate = true;
                    // 总全选是否开启 - 根据选中节点中为根节点的数量是否和源数据长度相等
                    let allCheck = val.filter(item => item[this.pid] == 0);
                    if (allCheck.length == this.self_to_data.length) {
                        // 关闭半选 开启全选
                        this.to_is_indeterminate = false;
                        this.to_check_all = true;
                    } else {
                        this.to_is_indeterminate = true;
                        this.to_check_all = false;
                    }
                } else {
                    this.to_disabled = true;
                    this.to_is_indeterminate = false;
                    this.to_check_all = false;
                }
            },
            // 左侧 数据筛选
            filterFrom(val) {
                this.$refs["from-tree"].filter(val);
            },
            // 右侧 数据筛选
            filterTo(val) {
                this.$refs["to-tree"].filter(val);
            }
        }
    };
</script>

<style scoped>
    .length {
        position: absolute;
        right: 15px;
        top: 0;
    }

    .el-tree {
        min-width: 100%;
        display: inline-block !important;
    }

    .transfer {
        position: relative;
        overflow: hidden;
    }

    .transfer-left {
        position: absolute;
        top: 0;
        left: 0;
    }

    .transfer-right {
        position: absolute;
        top: 0;
        right: 0;
    }

    .transfer-right-item {
        height: calc((100% - 41px) / 2);
    }

    .transfer-right-small {
        height: 41px;
    }

    .transfer-main {
        padding: 10px;
        height: calc(100% - 41px);
        box-sizing: border-box;
        overflow: auto;
    }

    .transfer-left,
    .transfer-right {
        border: 1px solid #ebeef5;
        width: 40%;
        height: 100%;
        box-sizing: border-box;
        border-radius: 5px;
        vertical-align: middle;
    }

    .transfer-center {
        position: absolute;
        top: 50%;
        left: 40%;
        width: 20%;
        transform: translateY(-50%);
        text-align: center;
    }

    .transfer-center-item {
        padding: 10px;
        overflow: hidden;
    }

    .address-list-center {
        height: 100%;
    }

    .address-list-center > .transfer-center-item {
        height: 50%;
        padding: 70px 10px 0;
        box-sizing: border-box;
        overflow: hidden;
    }

    .transfer-title {
        border-bottom: 1px solid #ebeef5;
        padding: 0 15px;
        height: 40px;
        line-height: 40px;
        color: #333;
        font-size: 16px;
        background-color: #f5f7fa;
    }

    .transfer-title .el-checkbox {
        margin-right: 10px;
    }

    .filter-tree {
        margin-bottom: 10px;
    }

    .transfer-title p {
        display: inline-block;
        position: relative;
        width: 50%;
        text-align: center;
        cursor: pointer;
    }
    .transfer-title p:first-child{
        border-right: 1px solid #ccc;
    }
    .transfer-title p.active{
        color: #6dbaf1;
    }
</style>
<style>
   .transfer2 .el-tree-node__content {
        height: 40px;
    }

   .transfer2 .el-checkbox__inner {
        top: 4px;
    }

   .transfer2 .el-tree-node__content .el-select {
        display: inline-block;
        margin-left: 10px;
    }
</style>
