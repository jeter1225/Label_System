<template>
  <div>
    <!-- title -->
    <div style="height: 100px; background-color: #fedc3d; display: flex; align-items: center">
      <div style="width: 100%; display: flex; justify-content: center">
        <div style="font-style: normal; font-weight: bold; font-size: 40px; line-height: 58px; color: #01abaa">
          Label System
        </div>
      </div>
    </div>
    <el-row :gutter="22" type="flex" justify="center" align="middle" style="margin-top: 10px">
      <el-col :span="4"><el-button type="danger" plain @click="download">下載</el-button></el-col>
      <el-col :offset="6" :span="8" style="display: flex; justify-content: end">
        <el-progress
          :text-inside="true"
          :stroke-width="26"
          :percentage="percentage"
          :format="format"
          style="width: 100%"
        ></el-progress>
      </el-col>
    </el-row>
    <el-row :gutter="22" type="flex" justify="center" align="middle" style="margin-top: 10px">
      <el-col :span="2">
        <el-button
          type="primary"
          icon="el-icon-arrow-left"
          @click="move(0)"
          :disabled="index === 0"
          class="switchBtn"
          plain
        ></el-button>
      </el-col>
      <el-col :span="14">
        <el-card shadow="always" style="width: 100%; height: 200px">
          <div slot="header">
            <span>ID: {{ fiDa[index]['sentence_id'] }}</span>
          </div>
          <div>
            {{ fiDa[index]['sentence'] }}
          </div>
        </el-card>
      </el-col>
      <el-col :span="2">
        <el-button
          type="primary"
          icon="el-icon-arrow-right"
          @click="move(1)"
          :disabled="index === fiDa.length - 1"
          class="switchBtn"
          plain
        ></el-button>
      </el-col>
    </el-row>
    <el-row :gutter="22" type="flex" justify="center" style="margin-top: 20px">
      <el-col :span="9">
        <div v-for="(item, idx) in predicates.check" :key="item.predication_id">
          <el-divider></el-divider>
          <el-row justify="space-between">
            <el-col :span="18" :style="selectionClass[idx]">
              <el-row>
                <el-col :span="6">Subject:</el-col>
                <el-col :span="16">{{ item.subject }}</el-col>
              </el-row>
              <el-row>
                <el-col :span="6">Relation:</el-col>
                <el-col :span="16">{{ item.relation }}</el-col>
              </el-row>
              <el-row>
                <el-col :span="6">Object:</el-col>
                <el-col :span="16">{{ item.object }}</el-col>
              </el-row>
            </el-col>
            <el-col :span="6">
              <el-checkbox v-model="keepList[idx]" border>保留</el-checkbox>
            </el-col>
          </el-row>
        </div>
      </el-col>
      <el-col :span="9">
        <div v-for="(item, idx) in predicates.extension" :key="item.predication_id">
          <el-divider></el-divider>
          <el-row justify="space-between">
            <el-col :span="18">
              <el-row>
                <el-col :span="6">Subject:</el-col>
                <el-col :span="16">{{ item.subject }}</el-col>
              </el-row>
              <el-row>
                <el-col :span="6">Relation:</el-col>
                <el-col :span="16">{{ item.relation }}</el-col>
              </el-row>
              <el-row>
                <el-col :span="6">Object:</el-col>
                <el-col :span="16">{{ item.object }}</el-col>
              </el-row>
            </el-col>
            <el-col :span="6">
              <el-checkbox v-model="coumpoundCheckList[idx]" border :disabled="disableList[idx]">新關係</el-checkbox>
            </el-col>
          </el-row>
        </div>
      </el-col>
    </el-row>
    <el-row :gutter="22" type="flex" justify="start" align="middle" style="margin-top: 10px">
      <el-col :offset="18" :span="3" style="display: flex; justify-content: end">
        <el-button type="warning" plain @click="dialogVisible = true">確認</el-button>
      </el-col>
    </el-row>
    <el-dialog width="70%" :visible.sync="dialogVisible" :show-close="false" :title="dialogTitle">
      <el-row justify="center">
        <el-col :offset="3" :span="18">
          <el-table :data="saveList" style="100%" v-if="saveList.length">
            <el-table-column prop="subject" label="Subject"> </el-table-column>
            <el-table-column prop="relation" label="Relation"> </el-table-column>
            <el-table-column prop="object" label="Object"> </el-table-column>
          </el-table>
          <span v-else>Nothing has benn sekected</span>
        </el-col>
      </el-row>
      <span slot="footer" class="dialog-footer">
        <el-button type="success" plain @click="confirmEditLabel">確認</el-button>
        <el-button type="info" @click="dialogVisible = false" plain>取消</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
import labelData from '../assets/total.json';
export default {
  name: 'LabelSystem',
  components: {},
  data() {
    return {
      index: 0,
      fiDa: labelData,
      value: true,
      predicates: {},
      keepList: [],
      coumpoundCheckList: [],
      disableList: [],
      linkList: [],
      selectionClass: [],
      reversSen: {},
      opacity: [],
      coverRelation: [],
      dialogVisible: false,
      saveList: [],
      dialogTitle: '',
      percentage: 0,
      localRelation: [],
    };
  },
  watch: {
    dialogVisible(newVal) {
      if (newVal) {
        this.keepList.map((o, idx) => {
          if (o) {
            this.saveList.push(this.predicates.check[idx]);
          }
        });
        this.coumpoundCheckList.map((o, idx) => {
          if (o) {
            this.saveList.push(this.predicates.extension[idx]);
          }
        });
      } else {
        this.saveList = [];
      }
    },
  },
  methods: {
    download() {
      const tmp = window.localStorage.getItem('localR');
      const jtmp = JSON.parse(tmp);
      let element = document.createElement('a');
      element.setAttribute('href', 'data:application/json;charset=utf-8,' + encodeURIComponent(tmp));
      element.setAttribute('download', `label_to_ID_${jtmp[jtmp.length - 1]['senID']}.json`);

      element.style.display = 'none';
      document.body.appendChild(element);

      element.click();
      document.body.removeChild(element);
      window.localStorage.setItem('localR', []);
    },
    format(per) {
      return `第${this.index + 1}筆，目前進度${per.toFixed(2)}％`;
    },
    confirmEditLabel() {
      this.dialogVisible = false;
      window.localStorage.setItem('labelID', this.index + 1);
      const tmp = {
        senID: this.fiDa[this.index]['sentence_id'],
        sentence: this.fiDa[this.index]['sentence'],
        predicates: this.saveList.map((o) => {
          return {
            subject: o.subject,
            relation: o.relation,
            object: o.object,
          };
        }),
      };
      this.localRelation.push(tmp);
      this.index += 1;
      window.localStorage.setItem('localR', JSON.stringify(this.localRelation));
    },
    move(i) {
      this.index = i ? this.index + 1 : this.index - 1;
      this.percentage = ((this.index + 1) / this.fiDa.length) * 100;
      this.sepCheck(this.fiDa[this.index]);
    },
    sepCheck(ob) {
      this.dialogTitle = `ID: ${this.fiDa[this.index]['sentence_id']}`;
      this.saveList = [];
      this.predicates = {};
      this.keepList = [];
      this.coumpoundCheckList = [];
      this.disableList = [];
      this.selectionClass = [];
      this.opacity = [];
      this.predicates = ob.predicates.reduce(
        (res, cur) => {
          if (!cur.check.length) {
            this.coumpoundCheckList.push(false);
            this.disableList.push(false);
            res.extension.push(cur);
          } else {
            this.keepList.push(false);
            this.opacity.push(0);
            res.check.push(cur);
          }
          return res;
        },
        { check: [], extension: [] }
      );

      this.predicates.check.map((o, idx) => {
        o.relationId = idx;
      });
      this.predicates.extension.map((o) => {
        o.subCompound = o.subject.split(', ');
        o.obCompound = o.object.split(', ');
      });
    },
  },
  mounted() {
    this.index = !window.localStorage.getItem('labelID') ? 0 : +window.localStorage.getItem('labelID');
    this.percentage = ((this.index + 1) / this.fiDa.length) * 100;
    this.localRelation = !window.localStorage.getItem('localR')
      ? []
      : JSON.parse(window.localStorage.getItem('localR'));
    // console.log(this.localRelation);
    this.sepCheck(this.fiDa[this.index]);
  },
};
</script>

<style lang="stylus">
.switchBtn
  width 100%
  height 200px
</style>
