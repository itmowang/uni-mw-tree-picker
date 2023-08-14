<template>
    <view class="content">
        <button @click="isShow=true">sb</button>
        <mwTreePicker :show="isShow" :data="listData" title="Hello" label="name" children="children" @submit="submit" @cancel="isShow=false" />
    </view>
</template>

<script>
import mwTreePicker from "@/components/mw-tree-picker/mw-tree-picker.vue"
export default {
    data() {
        return {
            isShow:false,
            title: 'Hello',
            listData: [
                {
                    id: 1,
                    name: '公司1',
                    children: [{
                        id: 11,
                        name: '研发部',
                        children: [{
                            id: 111,
                            name: '张三',
                        }, {
                            id: 112,
                            name: '李四',

                        }]
                    }, {
                        id: 12,
                        name: '综合部',
                    }]
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                },
                {
                    id: 3,
                    name: '公司3'
                }
            ]
        }
    },
    onLoad() {

    },
    methods: {
        submit(data) {
            console.log(data,"Hello")
        },
        showPicker() {
            
        }
    },
    components: {
        mwTreePicker
    }
}
</script>

<style>
.content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.logo {
    height: 200rpx;
    width: 200rpx;
    margin-top: 200rpx;
    margin-left: auto;
    margin-right: auto;
    margin-bottom: 50rpx;
}

.text-area {
    display: flex;
    justify-content: center;
}

.title {
    font-size: 36rpx;
    color: #8f8f94;
}
</style>
