<template>
  <div id="CompCont">
    <div id="Instruction">
      Build your answer by selecting the available options from the below list and using the up/down arrows to order them.
      <br />Note that you can unselect an answer to remove it.
    </div>
    <div id="InptAnswers">
      <span
        class="selOpt"
        :class="{'selected':answersArr.map(function(e) {
          return e.id;
        }).indexOf(sel.id)>-1}"
        v-for="sel in inputVarArr"
        :key="sel.id"
        @click="AddSel(sel)"
      >{{sel.lbl}}</span>
    </div>
    <div id="OutResult">
      <div class="answerRow" v-for="(ans,indx) in answersArr" :key="ans.id">
        <span @click="moveAnsw(false,indx)" class="srtBtn left">
          <span v-if="indx!=answersArr.length-1">&#8595;</span>
        </span>
        <span
          class="AnswOpt"
          v-bind:class="{ move_first: move_first==indx, move_second: move_second==indx, last_ans: indx==answersArr.length-1 }"
        >
          <span class="AnswIndx">{{indx+1}}</span>
          <!-- <span>--{{TabIndx[indx]}}</span> -->
          <span
            v-if="appSetup.FormatIndent"
            :style="'margin-left:'+(TabIndx[indx]*Indent)+'px;'"
            :class="'Indent'+TabIndx[indx]"
          >{{ans.lbl}}</span>
          <span v-else>{{ans.lbl}}</span>
        </span>
        <span @click="moveAnsw(true,indx)" class="srtBtn right">
          <span v-if="indx>0">&#8593;</span>
        </span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ClickBuild",
  data() {
    return {
      inputVarArr: [],
      appSetup,
      answersArr: [],
      TabIndx: [],
      Indent: 20,
      move_first: -1,
      move_second: -1,
      animation_delay: 500
    };
  },
  created() {
    let vueOBJ = this;
    inputVar
      .sort(() => Math.random() - 0.5)
      .forEach((elm, indx) => {
        vueOBJ.inputVarArr.push({ lbl: elm, id: indx });
      });
    this.inputVarArr;
    $("#mrForm").on("submit", function() {
      vueOBJ.SetOutput();
      return false;
    });
  },
  methods: {
    SetOutput() {
      let output = "";
      this.answersArr.forEach(elm => {
        output += elm.lbl;
      });
      $(".mrEdit").val(output);
    },
    GetIndex(indx) {
      return 0;
    },
    // GetIndex(indx) {
    //   let nrElem = this.answersArr.length;
    //   let mij, n;
    //   //impare
    //   if (nrElem % 2 == 1) {
    //     mij = Math.round(nrElem / 2); //3
    //     n = Math.abs(indx + 1 - mij);
    //   } else {
    //     mij = Math.round(nrElem / 2); //3
    //     if (indx + 1 > mij) {
    //       n = indx - mij;
    //     } else {
    //       n = mij - indx - 1;
    //     }
    //   }

    //   return mij - n;
    // },

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
#Instruction {
  color: grey;
  margin: 10px;
  padding: 5px;
  border-left: solid 4px orange;
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

.srtBtn {
  cursor: pointer;
  background-color: #49bfbc;
  color: white;
  width: 20px;
  display: inline-block;
  text-align: center;
}
.srtBtn.left {
  margin-right: 5px;
}

.srtBtn.right {
  margin-left: 5px;
}
</style>
