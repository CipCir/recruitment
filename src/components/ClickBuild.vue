<template>
  <div id="CompCont">
    <div id="Instruction">
      Build your answer by selecting the available options from the below list .
      <br />Note that you can drag and drop each option in order to sort your answers and you can remove a selection by clicking on
      <span
        class="closeBtn"
      >
        <i class="fas fa-window-close"></i>
      </span>
      or on the selection to remove it.
    </div>
    <div id="InptAnswers">
      <span
        class="selOpt"
        :class="[{'selected':answersArr.map(function(e) {
          return e.id;
        }).indexOf(sel.id)>-1},{'JScomOpt':sel.lbl.indexOf('//')==0}]"
        v-for="sel in inputVarArr"
        :key="sel.id"
        @click="AddSel(sel)"
      >{{sel.lbl}}</span>
    </div>
    <!-- <div id="OutResult" class="answerContainer"> -->
    <!-- <div class="answerRow" v-for="(ans,indx) in answersArr" :key="ans.id">
        <div class="srtBtn left">
          <span v-if="indx!=answersArr.length-1">
            <i class="fa fa-bars"></i>
          </span>
        </div>
        <div
          class="AnswOpt dns"
          v-bind:class="{ move_first: move_first==indx, move_second: move_second==indx, last_ans: indx==answersArr.length-1 }"
        >
          <span class="AnswIndx innText">{{indx+1}}</span>          
          <span
            v-if="appSetup.FormatIndent"
            :style="'margin-left:'+(TabIndx[indx]*Indent)+'px;'"
            class="innText"
            :class="'Indent'+TabIndx[indx]"
          >{{ans.lbl}}</span>
          <span v-else>{{ans.lbl}}</span>
        </div>
        <div class="srtBtn right">
          <span v-if="indx>0">
            <i class="fa fa-bars"></i>
          </span>
        </div>
    </div>-->
    <!-- </div> -->
    <draggable
      id="OutResult"
      v-model="answersArr"
      @choose="moved($event)"
      @start="drag=true"
      @end="drag=false,UpdateTabIndex()"
    >
      <div
        class="answerRow"
        v-bind:class="{ move_first: move_first==indx, move_second: move_second==indx, last_ans: indx==answersArr.length-1}"
        v-for="(element,indx) in answersArr"
        :key="element.id"
      >
        <!-- {{element.lbl}} -->
        <span class="srtBtn">
          <i class="fas fa-sort"></i>
        </span>
        <span class="AnswIndx innText">{{indx+1}}</span>
        <span
          v-if="appSetup.FormatIndent"
          :style="'margin-left:'+(TabIndx[indx]*Indent)+'px;'"
          class="innText"
          :class="'Indent'+TabIndx[indx]"
        >{{element.lbl}}</span>
        <span :class="{'JScom':element.lbl.indexOf('//')==0}" v-else>{{element.lbl}}</span>
        <div class="BtnCont right">
          <span class="closeBtn" @click="RemoveOpt(element)">
            <i class="fas fa-window-close"></i>
          </span>
          <span class="srtBtn">
            <i class="fas fa-sort"></i>
          </span>
        </div>
      </div>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";
export default {
  name: "ClickBuild",
  components: { draggable },
  data() {
    return {
      inputVarArr: [],
      appSetup,
      answersArr: [],
      TabIndx: [],
      Indent: 20,
      move_first: -1,
      move_second: -1,
      animation_delay: 500,
      was_updated: true
    };
  },
  created() {
    let vueOBJ = this;
    inputVar.forEach((elm, indx) => {
      vueOBJ.inputVarArr.push({ lbl: elm, id: indx });
    });
    vueOBJ.inputVarArr.sort(() => Math.random() - 0.5);
    this.inputVarArr;

    $("#mrForm").on("submit", function() {
      vueOBJ.SetOutput();
      // return false;
    });
  },
  methods: {
    moved(ev) {
      this.move_first = ev.oldIndex;
    },
    SetOutput() {
      let output = "#";
      this.answersArr.forEach(elm => {
        output += elm.id + "#";
      });
      $(".mrEdit").val(output);
    },
    RemoveOpt(sel) {
      let valIndx = this.answersArr
        .map(function(e) {
          return e.id;
        })
        .indexOf(sel.id);
      this.answersArr.splice(valIndx, 1);
      this.move_first = -1;
    },

    AddSel(sel) {
      // let valIndx = this.answersArr.indexOf(sel);
      let valIndx = this.answersArr
        .map(function(e) {
          return e.id;
        })
        .indexOf(sel.id);
      if (valIndx > -1) {
        this.answersArr.splice(valIndx, 1);
        this.UpdateTabIndex();
      } else {
        // if (this.answersArr.length < 10) {
        this.answersArr.push(sel);
        this.UpdateTabIndex();
        // }
      }
    },
    UpdateTabIndex() {
      this.move_first = -1;
      let vueObj = this;
      let Tx = 0;
      let lastUp = false;
      this.TabIndx = [];

      this.answersArr.forEach(function(elm, indx) {
        if (elm.lbl.substring(0, 2) == "</") {
          if (!lastUp) {
            Tx--;
          }
          lastUp = false;
        } else {
          if (lastUp) {
            Tx++;
          }
          lastUp = true;
        }
        if (Tx < 0) {
          Tx = 0;
        }
        vueObj.TabIndx[indx] = Tx;
      });
    },
    moveAnsw(isUpInList, currentPos) {
      if (isUpInList) {
        if (currentPos > 0) {
          this.animate_switch(currentPos, currentPos - 1);
          let vueOBJ = this;
          setTimeout(function() {
            // console.log('up in list',currentPos, currentPos-1);
            let temp_val = vueOBJ.answersArr[currentPos];
            // this.answersArr[currentPos]=this.answersArr[currentPos-1];
            vueOBJ.$set(
              vueOBJ.answersArr,
              currentPos,
              vueOBJ.answersArr[currentPos - 1]
            );
            // vueOBJ.update_vals(currentPos, vueOBJ.answersArr[currentPos - 1]);
            // this.answersArr[currentPos-1]=temp_val;
            vueOBJ.$set(vueOBJ.answersArr, currentPos - 1, temp_val);
            vueOBJ.UpdateTabIndex();
            // vueOBJ.update_vals(currentPos - 1, temp_val);
          }, vueOBJ.animation_delay);
        } else {
          // console.log("cannot move up first elem");
          // console.log(this.attempts);
          return false;
        }
      } else {
        // console.log('down att');

        let vueOBJ = this;
        if (currentPos < this.answersArr.length - 1) {
          this.animate_switch(currentPos, currentPos + 1);
          setTimeout(function() {
            // console.log('up in list',currentPos, currentPos+1);
            var temp_val = vueOBJ.answersArr[currentPos];
            // this.answersArr[currentPos]=this.answersArr[currentPos-1];
            vueOBJ.$set(
              vueOBJ.answersArr,
              currentPos,
              vueOBJ.answersArr[currentPos + 1]
            );
            // vueOBJ.update_vals(currentPos, vueOBJ.answersArr[currentPos + 1]);
            // this.answersArr[currentPos-1]=temp_val;
            vueOBJ.$set(vueOBJ.answersArr, currentPos + 1, temp_val);
            vueOBJ.UpdateTabIndex();
            // vueOBJ.update_vals(currentPos + 1, temp_val);
          }, vueOBJ.animation_delay);
        } else {
          // console.log("cannot move down last elem");
          return false;
        }
      }
    },
    animate_switch(pos1, pos2) {
      // console.log("start");
      this.move_first = pos1;
      this.move_second = pos2;
      var vueOBJ = this;
      setTimeout(function() {
        // console.log("end");
        vueOBJ.move_first = pos2;
        vueOBJ.move_second = pos1;
      }, vueOBJ.animation_delay);
      setTimeout(function() {
        // console.log("end");
        vueOBJ.move_first = -1;
        vueOBJ.move_second = -1;
      }, 2 * vueOBJ.animation_delay);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.answerRow {
  cursor: pointer;
  margin: 5px;
  background: ghostwhite;
}
.answerRow:hover {
  background: rgb(218, 218, 252);
}
#Instruction {
  color: grey;
  margin: 10px;
  padding: 5px;
  border-left: solid 4px orange;
}
#InptAnswers {
  margin: 10px;
}
.AnswIndx {
  width: 25px;
  display: inline-block;
  margin-right: 5px;
}
.Indent0 {
  color: black;
}
.Indent1 {
  color: rgb(234, 108, 0);
}
.Indent2 {
  color: rgb(0, 112, 208);
}
.Indent3 {
  color: rgb(33, 136, 47);
}
.Indent4 {
  color: rgb(212, 192, 46);
}
.Indent5 {
  color: rgb(102, 6, 37);
}
.Indent6 {
  color: rgb(21, 5, 255);
}
.Indent7 {
  color: rgb(18, 171, 0);
}
#OutResult {
  font-size: large;
  margin-left: 10px;
  padding: 10px;
  margin-top: 10px;
  min-width: 80%;
}
.AnswOpt {
  background: aliceblue;
  margin: 3px 0;
  width: 90%;
  display: inline-block;
}
.selOpt {
  background: #a9e0a9;
  padding: 3px;
  margin: 3px;
  display: inline-block;
  border-radius: 5px;
  cursor: pointer;
  font-weight: bold;
  font-size: large;
}
.selected {
  background: #a9aee0;
}
.move_first {
  /* border-color: #0cb2b052;
  background: #0cb2b052; */
  border-color: rgba(12, 178, 175, 0.5);
  background: rgba(12, 178, 175, 0.5);
}
.move_second {
  /* border-color: #4b64ae52;
  background: #4b64ae52; */
  border-color: rgba(75, 100, 174, 0.5);
  background: rgba(75, 100, 174, 0.5);
}
.move_first,
.move_second {
  /* color: white; */
  font-weight: bold;
  -moz-transition: all 0.1s ease-in-out;
  -o-transition: all 0.1s ease-in-out;
  -webkit-transition: all 0.1s ease-in-out;
  transition: all 0.1s ease-in-out;
  opacity: 0.7;
}
.JScom {
  color: #00bbb6;
}
.JScomOpt {
  color: #025d5a;
}
.BtnCont {
  /* display: inline-block; */
  text-align: center;
  margin-left: 5px;
  float: right;
}
.srtBtn {
  background: rgb(218, 218, 252);
  color: #999;
  width: 20px;
  display: inline-block;
  text-align: center;
  margin: 0 3px;
}
.closeBtn {
  margin-left: 5px;
  color: #e07575;
  width: 19px;
  height: 19px;
  /* transition: transform 0.2s; */
}
.closeBtn > i:hover {
  transform: scale(1.2);
}
/* .srtBtn.left {
  margin-right: 5px;
} */
</style>
