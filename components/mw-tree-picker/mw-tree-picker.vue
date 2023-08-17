<template>
  <!-- 遮罩层 -->
  <div class="mask" v-if="show">
    <!-- picker-->
    <div class="content" :style="'height:' + height">
      <div class="header">
        <div class="cancel" @click="cancel">取消</div>
        <div class="title">
          {{ title }}
        </div>
        <div class="submit" @click="submit">确定</div>
      </div>
      <!-- 横线 -->
      <div class="line"></div>
      <!-- tree 可下拉 可全展开 可勾选 原生 -->
      <div class="tree">
        <ul>
          <template v-for="item in datasource">
            <li>
              <div class="item-text">
                <div
                  class="label"
                  :style="{ paddingLeft: item.level * 20 + 'px' }"
                >
                  {{ item[label] }}
                </div>
                <div class="check-box">
                  <div
                    :class="item.check ? 'check-active' : 'check'"
                    @click="toggleItem(item)"
                  ></div>
                </div>
              </div>
            </li>
            <div style="margin-left: 20px">
              <template v-if="item.children">
                <li v-for="child in item.children" :key="child.id">
                  <div
                    class="item-text"
                    :style="{ marginLeft: (item.level + 1) * 20 + 'px' }"
                  >
                    <div class="label">{{ child[label] }}</div>
                    <div class="check-box">
                      <div
                        :class="child.check ? 'check-active' : 'check'"
                        @click="toggleItem(child)"
                      ></div>
                    </div>
                  </div>
                </li>
              </template>
            </div>
          </template>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "mw-tree-picker",
  props: {
    show: {
      type: Boolean,
      default: false,
    },
    height: {
      type: String,
      default: "50%",
    },
    title: {
      type: String,
      default: "请选择",
    },
    data: {
      type: Array,
      default: () => [],
    },
    label: {
      type: String,
      default: "label",
    },
    children: {
      type: String,
      default: "children",
    },
  },
  watch: {
    show: {
      handler(newData) {
        this.datasource = this.flattenData(this.data);
      },
      deep: true,
    },
  },

  data() {
    return {
      datasource: [],
    };
  },
  methods: {
    // 取消
    cancel() {
      // emit
      this.$emit("cancel");
    },
    // 根据传递进来的数组 和动态的key 和this.datasource 匹配上的 item[key] 等于 datas中的某个id 说明这个item是选中的 
    checkData(datas, key) {
      this.$nextTick(() => {
       this.datasource=  this.datasource.map((item) => {
           if(datas.some((data) => data[key] === item[key])) {
             item.check = true;
            //  如果勾选了子节点，那么它的父节点也要勾选
            if (item.level > 1) {
              const parentIndex = item.index.split("-").slice(0, -1).join("-");
              const parent = this.datasource.find(
                (item) => item.index === parentIndex
              );
              parent.check = true;
            }
           }
           return item;
        });
      });
    },

    // 通过已经勾选的数据 匹配原数据data 只不过check要通过判断形成新的
    getCheckedData() {
      const checkedData = this.datasource.filter(
        (item) => item.check && !item[this.children]
      );
      const checkedIds = checkedData.map((item) => item.id);
      const data = JSON.parse(JSON.stringify(this.data));
      return this.matchData(data, checkedIds);
    },

    // 通过已经勾选的数据 匹配原数据data 只不过check要通过判断形成新的
    matchData(data, checkedIds) {
      return data.map((item) => {
        if (checkedIds.includes(item.id)) {
          item.check = true;
        }
        if (item[this.children]) {
          item[this.children] = this.matchData(item[this.children], checkedIds);
        }
        return item;
      });
    },

    // 提交
    submit() {
      // 要勾选的数据 如果有子节点 父级节点不要
      const checkedData = this.datasource.filter(
        (item) => item.check && !item[this.children]
      );
      // emit
      this.$emit("submit", { checkedData, datasource: this.getCheckedData() });
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
      this.$forceUpdate();
    },
    // 将指定元素的所有子元素勾选
    checkAllChildren(item) {
      // 如果勾选了子节点，那么它的父节点也要勾选
      if (item.level > 1) {
        const parentIndex = item.index.split("-").slice(0, -1).join("-");
        const parent = this.datasource.find(
          (item) => item.index === parentIndex
        );
        parent.check = true;
      }

      this.datasource.forEach((child) => {
        if (child.index.startsWith(item.index) && child.index !== item.index) {
          child.check = true;
        }
      });
    },
    // 将指定元素的所有子元素取消勾选
    uncheckAllChildren(item) {
      //当子节点所有都取消勾选，那么它的父节点也要取消勾选
      if (item.level > 1) {
        const parentIndex = item.index.split("-").slice(0, -1).join("-");
        const parent = this.datasource.find(
          (item) => item.index === parentIndex
        );
        const siblings = this.datasource.filter(
          (item) =>
            item.index.startsWith(parentIndex) && item.index !== parentIndex
        );
        if (siblings.every((item) => !item.check)) {
          parent.check = false;
        }
      }

      this.datasource.forEach((child) => {
        if (child.index.startsWith(item.index) && child.index !== item.index) {
          child.check = false;
        }
      });
    },
    // 递归成一维 并且都有唯一的index 层次分明 有父子关系函数
    flattenData(data, parentIndex = "") {
      let flattenedData = [];
      data.forEach((item, index) => {
        const currentIndex = parentIndex
          ? `${parentIndex}-${index}`
          : `${index}`;
        const { id, name, children, ...rest } = item;
        flattenedData.push({
          id,
          name,
          index: currentIndex,
          level: parentIndex ? parentIndex.split("-").length + 1 : 1,
          ...rest,
        });
        if (item[this.children]) {
          flattenedData = flattenedData.concat(
            this.flattenData(item[this.children], currentIndex)
          );
        }
      });
      return flattenedData;
    },
  },
};
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
  /* padding: 0 10px; */
  width: 90%;
  margin: 0 auto;
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
  color: #409eff;
}

.check-active {
  background-color: #409eff;
  color: #fff;
  width: 20px;
  height: 20px;
  border: 1px solid #409eff;
  border-radius: 2px;
  float: right;
  margin-right: 10px;
}

/* 伪类 伪元素 选中的有对勾 */
.check-active::after {
  content: "";
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

.tree {
  overflow-y: auto;
  width: 100%;
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
