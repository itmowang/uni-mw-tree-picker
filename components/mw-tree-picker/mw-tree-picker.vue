<template>
    <!-- 遮罩层 -->
    <div class="mask" v-if="show">
        <!-- picker-->
        <div class="content" :style="`height:${height}`" >
            <div class="header">
                <div class="cancel" @click="cancel">
                    取消
                </div>
                <div class="title">
                    {{title}}
                </div>
                <div class="submit" @click="submit">
                    确定
                </div>
            </div>
            <!-- 横线 -->
            <div class="line"></div>
            <!-- tree 可下拉 可全展开 可勾选 原生 -->
            <div class="tree">
                <ul>
                    <template v-for="item in datasource">
                        <li>
                            <div class="item-text">
                                <div class="label" :style="{ paddingLeft: item.level * 20 + 'px' }">{{ item[label] }}</div>
                                <div class="check-box">
                                    <div :class="`${item.check ? 'check-active' : 'check'}`" @click="toggleItem(item)">
                                    </div>
                                </div>
                            </div>
                        </li>
                        <div style="margin-left: 20px;">
                            <template v-if="item.children">
                                <li v-for="child in item.children" :key="child.id">
                                    <div class="item-text" :style="{ marginLeft: (item.level + 1) * 20 + 'px' }">
                                        <div class="label">{{ child[label] }}</div>
                                        <div class="check-box">
                                            <div :class="`${child.check ? 'check-active' : 'check'}`"
                                                @click="toggleItem(child)">
                                            </div>
                                        </div>
                                    </div>
                                </li>
                            </template>
                        </div>
                    </template>

                    <!-- <li v-for="item in datasource" :key="item.id">
                        <div class="item-text">
                            <div :class="`${item.check ? 'check-active' : `check`}`" @click="toggleItem(item)"> </div>
                            <div>{{ item[label] }}</div>
                        </div>
                    </li> -->

                    <!-- <li v-for="item in data" :key="item.id">
                        <div class="item-text">
                            <div :class="`${item.check ? 'check-active' : `check`}`" @click="toggleItem(item)"> </div>
                            <div>{{ item[label] }}</div>
                        </div>
                        <ul v-if="item.children && item.children.length > 0">
                            <li v-for="child in item.children" :key="child.id">
                                <div class="item-text">
                                    <div :class="`${item.check ? 'check-active' : `check`}`" @click="toggleItem(child)">
                                    </div>
                                    <div>{{ child[label] }}</div>
                                </div>
                            </li>
                        </ul>
                    </li> -->
                </ul>
            </div>
        </div>

    </div>
</div></template>
<script>
export default {
    name: "mw-tree-picker",
    props: {
        show: {
            type: Boolean,
            default: false
        },
        height: {
            type: String,
            default: '50%'
        },
        title: {
            type: String,
            default: '请选择'
        },
        data: {
            type: Array,
            default: () => []
        },
        label: {
            type: String,
            default: 'label'
        },
        children: {
            type: String,
            default: 'children'
        },
    },
    data() {
        return {
            datasource: []
        }
    },
    methods: {
        // 取消
        cancel() {
            // emit
            this.$emit('cancel');
        },
        // 提交
        submit(){
            // 提交处理已经勾选的值
            const checkedData = this.datasource.filter(item => item.check);
            // emit
            this.$emit('submit', checkedData);
        },
        // 选中的处理
        toggleItem(item) {
            // 选中的处理
            item.check = !item.check;
            // 如果勾选当前节点，那么它的所有子节点都勾选
            if (item.check) {
                this.checkAllChildren(item);
            } else {
                this.uncheckAllChildren(item);
            }


            this.$forceUpdate()
        },
        // 将指定元素的所有子元素勾选
        checkAllChildren(item) {
            this.datasource.forEach(child => {
                if (child.index.startsWith(item.index) && child.index !== item.index) {
                    child.check = true;
                }
            });
        },
        // 将指定元素的所有子元素取消勾选
        uncheckAllChildren(item) {
            this.datasource.forEach(child => {
                if (child.index.startsWith(item.index) && child.index !== item.index) {
                    child.check = false;
                }
            });
        },
        // 递归成一维 并且都有唯一的index 层次分明 有父子关系函数
        flattenData(data, parentIndex = '') {
            let flattenedData = [];
            data.forEach((item, index) => {
                const currentIndex = parentIndex ? `${parentIndex}-${index}` : `${index}`;
                const { id, name, children, ...rest } = item;
                flattenedData.push({
                    id,
                    name,
                    index: currentIndex,
                    level: parentIndex ? parentIndex.split('-').length + 1 : 1,
                    ...rest
                });
                if (children) {
                    flattenedData = flattenedData.concat(this.flattenData(children, currentIndex));
                }
            });
            return flattenedData;
        },
    },
    mounted() {
        this.datasource = this.flattenData(this.data);
    },
}
</script>
<style scoped>
.line {
    width: 100%;
    height: 1px;
    background-color: #e5e5e5;
}

.content {
    display: flex;
    flex-direction: column;
    width: 100%;
    background-color: #fff;
    min-height: 30%;
    position: absolute;
    bottom: 0;
}

.header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    height: 50px;
    min-height: 50px;
    background-color: #fff;
    padding: 0 10px;
}

.cancel {
    font-size: 14px;
    color: #333;
}

.title {
    font-size: 16px;
    color: #333;
}

.submit {
    font-size: 14px;
    color: #409EFF;
}

.check-active {
    background-color: #409EFF;
    color: #fff;
    width: 20px;
    height: 20px;
    border: 1px solid #409EFF;
    border-radius: 2px;
    float: right;
    margin-right: 10px;
}

/* 伪类 伪元素 选中的有对勾 */
.check-active::after {
    content: '';
    display: block;
    width: 10px;
    height: 10px;
    border: 1px solid #fff;
    border-top: none;
    border-right: none;
    transform: rotate(-50deg);
    position: relative;
    top: 2px;
    left: 4px;

}

.check {
    width: 20px;
    height: 20px;
    border: 1px solid #e5e5e5;
    border-radius: 2px;
    margin-right: 10px;
    background-color: #fff;
    cursor: pointer;
    float: right;
}

.tree{
    overflow-y: auto;
}
.tree ul {
    list-style: none;
    padding: 10px;
}

.tree ul li {
    position: relative;
}

.tree ul li .item-text {
    display: flex;
    height: 40px;
    font-size: 14px;
    color: #333;
    cursor: pointer;
    width: 100%;
}

.tree ul li .item-text .check-box {
    width: 100%;
    position: absolute;
    right: 0;
}

.mask {
    display: flex;
    flex-direction: column;
    height: 100%;
    width: 100%;
    position: fixed;
    top: 0;
    left: 0;
    z-index: 999;
    background-color: rgba(0, 0, 0, 0.5);
}
</style>