<!-- 程序题 组件 -->
<template>
  <div class="pq-body">

    <!-- 题目描述 -->
    <div v-show="isFullScreen === false" class="pq-question">

      <!-- 标题 & 分值 -->
      <div class="pq-question-top">
        <div class="pq-question-title">最长字符串</div>
        <div class="pq-question-top-score">5分</div>
      </div>

      <!-- 题目内容 -->
      <div class="pq-question-content">求出5个字符串中最长的字符串。每个字符串长度在100以内，且全为小写字母。
      </div>

      <!-- 输入示例 -->
      <div class="pq-question-IO-title">输入示例：</div>
      <div class="pq-question-IO-content">one two three four five</div>

      <!-- 输出示例 -->
      <div class="pq-question-IO-title">输出示例：</div>
      <div class="pq-question-IO-content">three</div>

      <!-- 题目限制 -->
      <div class="pq-question-restriction">
        <div class="pq-question-restriction-item">
          <div>代码长度限制</div>
          <div>16 KB</div>
        </div>
        <div class="pq-question-restriction-item">
          <div>时间限制</div>
          <div>400 ms</div>
        </div>
        <div class="pq-question-restriction-item">
          <div>内存限制</div>
          <div>64 MB</div>
        </div>
      </div>
    </div>

    <!-- OJ -->
    <div v-show="isFullScreen === false" class="pq-editor">
      <editor ref="ojEditor" @fullScreen="isFullScreen = true" :isFullScreen="isFullScreen"></editor>
    </div>

    <!-- 全屏OJ -->
    <div v-if="isFullScreen" class="pq-full-screen-editor">
      <editor @fullScreen="isFullScreen = false" :isFullScreen="isFullScreen" />
    </div>

    <!-- 提交弹窗 -->
    <el-dialog title="上次提交结果" :visible.sync="isShowResult">
      <div style="margin-bottom: 12px">系统耗时: <span style="color: #4c8f2a;font-weight: 1000">{{runTime/1000}}</span> 秒</div>
      <!-- 测试点 -->
      <el-table  type="index" v-loading="isRun" :data="codeRunResult" :cell-style="setStateColor" max-height="340px">
<!--        <el-table-column prop="date" label="编号" width="160"></el-table-column>-->
        <el-table-column label="编号" width="100" type="index"></el-table-column>
        <el-table-column prop="state" label="状态" width="120" class-name="pq-result-state"></el-table-column>
        <el-table-column prop="score" label="分数" width="120"></el-table-column>
        <el-table-column prop="compiler" label="编译器" width="120"></el-table-column>
        <el-table-column prop="memory" label="内存" width="120"></el-table-column>
        <el-table-column prop="spentTime" label="用时" width="120"></el-table-column>
      </el-table>

      <!-- 代码 -->
<!--      <div class="pq-result-code">-->
<!--        <div class="pq-result-code-text">代码</div>-->
<!--        <monaco-editor @editorMounted="(monacoEditor) => {this.monacoEditor = monacoEditor}" readOnly/>-->
<!--      </div>-->

      <!-- 确定、取消 -->
      <template #footer>
        <el-button @click="$emit('changeShowProgrammingResult', false)">取 消</el-button>
        <el-button type="primary" @click="$emit('changeShowProgrammingResult', false)">确 定</el-button>
      </template>
    </el-dialog>

  </div>
</template>

<script>
import editor from "@/components/editor"
import monacoEditor from "@/components/monacoEditor";
import {codeTest} from "@/api/code.api";
import {mapState} from "vuex";

export default {
  name: "programmingQuestion",
  components: {editor, monacoEditor},
  props: {
    isShowProgrammingResult: Boolean, // 是否显示编程题提交结果
  },

  data() {
    return {
      isFullScreen: false, // 是否全屏
      resultDatas: [], // 提交结果数据
      monacoEditor: {}, // 编辑器
    }
  },

  computed: {

    ...mapState({
      codeRunResult:(state)=>state.codeRunResult,
      runTime:(state)=>state.runTime,
      isRun:(state)=>state.isRun
    }),

    // 是否显示编程题提交结果
    isShowResult: {
      get() {
        return this.isShowProgrammingResult;
      },
      set(value) {
        this.$emit('changeShowProgrammingResult', value);
      }
    },
  },

  watch: {
    isShowResult(value) {
      this.$emit('changeShowProgrammingResult', value);
    },
  },

  created() {
    // this.getResultDatas();
  },

  methods: {

    /**
     * 触发子组件方法，打印编辑器中的代码,提交代码并获取返回结果渲染
     */
    consoleCode(){
      this.$refs.ojEditor.getCode()
    },

    /**
     * 设置状态列文字颜色
     */
    setStateColor({row, columnIndex}) {
      if(columnIndex === 1) {
        if(row.state === "答案正确") {
          return "color: #F56C6C";
        } else if(row.state === "编译错误") {
          return "color: #1249ff";
        } else {
          return "color: green";
        }
      }
    },

    /**
     * 获取提交结果数据
     */
    getResultDatas() {
      const resultState = ["答案正确", "编译错误", "答案错误", "运行超时", "运行时错误"];
      for (let i = 0; i < 10; i++) {
        this.resultDatas.push({
          date: "2023/03/09 10:20", // 提交时间
          state: resultState[Math.floor(Math.random() * resultState.length)], // 状态
          score: 20, // 分数
          compiler: "C++", // 编译器
          memory: "10KB", // 内存
          spentTime: "10ms" // 用时
        })
      }
    },

  }
}
</script>

<style scoped>
.pq-body {
  width: 1792px;
  height: 843px;
  display: flex;
  justify-content: space-between;
}

.pq-question {
  width: 892px;
  background-color: white;
  padding: 24px 32px;
  box-sizing: border-box;
  border-radius: 8px 0 0 0;
  border: 1px solid #D9DCE2;
  border-bottom: none;
  margin-right: 4px;
}

.pq-question-top {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.pq-question-title {
  font-size: 20px;
  font-family: SourceHanSansSC-Medium, SourceHanSansSC;
  font-weight: 500;
  color: #262626;
  margin-right: 8px;
}

.pq-question-top-score {
  width: 45px;
  height: 20px;
  background: #F4F5F7;
  border-radius: 10px;
  font-size: 14px;
  font-family: SourceHanSansSC-Regular, SourceHanSansSC;
  font-weight: 400;
  color: #BFBFBF;
  line-height: 20px;
  display: flex;
  justify-content: center;
}

.pq-question-content {
  font-size: 16px;
  font-family: SourceHanSansSC-Regular, SourceHanSansSC;
  font-weight: 400;
  color: #595959;
  line-height: 32px;
  margin-bottom: 32px;
}

.pq-question-IO-title {
  font-size: 20px;
  font-family: SourceHanSansSC-Medium, SourceHanSansSC;
  font-weight: 500;
  color: #262626;
  margin-bottom: 20px;
}

.pq-question-IO-content {
  font-size: 16px;
  font-family: SourceHanSansSC-Regular, SourceHanSansSC;
  font-weight: 400;
  color: #262626;
  line-height: 24px;
  margin-bottom: 32px;
}

.pq-question-restriction {
  width: 828px;
  height: 112px;
  background: #FAFAFB;
  border-radius: 8px;
  border: 1px solid #F0F1F4;
  padding: 24px;
  box-sizing: border-box;
}

.pq-question-restriction-item {
  display: flex;
  justify-content: space-between;
  font-size: 16px;
  font-family: SourceHanSansSC-Regular, SourceHanSansSC;
  font-weight: 400;
  color: #262626;
  line-height: 24px;
}

.pq-editor {
  width: 896px;
  background-color: #fff;
  box-sizing: border-box;
}

::v-deep .el-dialog__title {
  font-size: 16px;
  font-family: PingFangSC-Semibold, PingFang SC;
  font-weight: 600;
  color: #262626;
  line-height: 22px;
}

::v-deep .el-table th.el-table__cell {
  background: #F4F5F7;
  font-size: 14px;
  font-family: SourceHanSansSC-Medium, SourceHanSansSC;
  font-weight: 500;
  color: #262626;
}

::v-deep .el-table__body {
  font-size: 14px;
  font-family: SourceHanSansSC-Regular, SourceHanSansSC;
  font-weight: 400;
  color: #262626;
}

.pq-result-code {
  display: flex;
  height: 222px;
  margin-top: 8px;
}

.pq-result-code-text {
  width: 36px;
  font-size: 16px;
  font-family: SourceHanSansSC-Medium, SourceHanSansSC;
  font-weight: 500;
  color: #262626;
  margin-right: 16px;
}

.pq-result-editor {
  width: 870px;
  height: 222px;
  border-radius: 4px;
  border: 1px solid #D9DCE2;
}

::v-deep .pq-result-code .et-editor{
  height: 222px !important;
}

.pq-full-screen-editor {
  width: 100%;
  height: 100%;
}

::v-deep .pq-full-screen-editor .et-editor {
  width: 100%;
  height: 820px;
}

</style>
