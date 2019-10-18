<template>
  <div id="CompCont">
    <div id="InptAnswers">
      <span
        class="selOpt"
        :class="{'selected':answersArr.indexOf(sel)>-1}"
        v-for="(sel,indx) in inputVar"
        :key="indx"
        @click="AddSel(sel)"
      >{{sel}}</span>
    </div>
    <div id="OutResult">
      <div class="answerRow" v-for="(ans,indx) in answersArr" :key="ans">
        <span @click="moveAnsw(true,indx)" class="srtBtn left">&#8593;</span>
        <span class="AnswOpt">
          <span>{{indx+1}}</span>
          <span :class="'Indent'+parseInt(indx+1)">{{ans}}</span>
        </span>
        <span @click="moveAnsw(false,indx)" class="srtBtn right">&#8595;</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ClickBuild",
  data() {
    return {
      inputVar,
      answersArr: [],
      move_first: -1,
      move_second: -1,
      animation_delay: 500
    };
  },
  created() {
    this.inputVar.sort(() => Math.random() - 0.5);
  },
  methods: {
    AddSel(sel) {
      let valIndx = this.answersArr.indexOf(sel);
      if (valIndx > -1) {
        this.answersArr.splice(valIndx, 1);
      } else {
        if (this.answersArr.length < 10) {
          this.answersArr.push(sel);
        }
      }
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
            // vueOBJ.update_vals(currentPos + 1, temp_val);
          }, vueOBJ.animation_delay);
        } else {
          // console.log("cannot move down last elem");
          return false;
        }
      }
      this.attempts++;
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
.Indent1,
.Indent10 {
  margin-left: 10px;
  color: black;
}
.Indent2,
.Indent9 {
  margin-left: 20px;
  color: rgb(240, 15, 15);
}
.Indent3,
.Indent8 {
  margin-left: 30px;
  color: rgb(90, 161, 46);
}
.Indent4,
.Indent7 {
  margin-left: 40px;
  color: rgb(12, 49, 236);
}
.Indent5,
.Indent6 {
  margin-left: 50px;
  color: rgb(190, 19, 224);
}
#OutResult {
  font-size: large;
  margin-left: 10px;
}
.AnswOpt {
  background: aliceblue;
  margin: 5px 0;
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
.answerRow {
  /* background: #aaa; */
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: center;
}
.answerRow > .srtBtn {
  flex-grow: 1;
  /* flex: 1 1 30%; */
}
.answerRow > .answer {
  flex-grow: 1;
  /* flex: 1 1 30%; */
}
.srtBtn {
  /* background-color: #fff4e6; */
  /* border: 1px solid #f76707; */
  font-size: 30px;
  background-color: #49bfbc;
  color: white;
}
</style>
