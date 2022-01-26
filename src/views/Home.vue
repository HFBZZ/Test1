<template>
  <div class="main">
    <div class="cont-con">
      <div class="cls-con title-con">
        <div
          class="title"
          v-for="(data, i) in dataArr"
          @click="nav_click(i)"
          :style="data.style"
        >
          {{ data.title }}
        </div>
      </div>
      <div id="myChart" :style="{ width: '100%', height: '300px' }"></div>
      <div class="cls-con title-con" style="justify-content: center">
        <div class="title">STATES_AGENT</div>
        <div id="myChart2" :style="{ width: '100%', height: '300px' }"></div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";

@Options({
  components: {},
})
export default class Home extends Vue {
  
  times = 0;

  dataArr = [
    {
      title: "FDD_ECLSS_DUST",
      data: require("@/assets/FDD_ECLSS_DUST.json"),
      style: { "border-color": "rgb(226, 209, 55)", "box-shadow": "" },
    },
    {
      title: "FDD_ECLSS_PAINT",
      data: require("@/assets/FDD_ECLSS_PAINT.json"),
      style: { "border-color": "#868686", "box-shadow": "" },
    },
    {
      title: "FDD_NPG_DUST",
      data: require("@/assets/FDD_NPG_DUST.json"),
      style: { "border-color": "#868686", "box-shadow": "" },
    },
    {
      title: "FDD_SPG_DUST",
      data: require("@/assets/FDD_SPG_DUST.json"),
      style: { "border-color": "#868686", "box-shadow": "" },
    },
    {
      title: "FDD_STR_DMG",
      data: require("@/assets/FDD_STR_DMG.json"),
      style: { "border-color": "#868686", "box-shadow": "" },
    },
  ];
  index = 0;
  STATES_AGENT = require("@/assets/STATES_AGENT.json");
  mounted() {
    //this.wsapi();
    let myChart = this.$echarts.init(document.getElementById("myChart"));
    let myChart2 = this.$echarts.init(document.getElementById("myChart2"));
    setInterval(() => {
      // 绘制图表
      //myChart.setOption(this.get_chart_data());
      myChart2.setOption(this.get_chart2_data());
      this.check_data();
    }, 100);
  }
  get_chart2_data() {
    let arr = this.STATES_AGENT.slice(this.times, this.times + 100);
    let xArr: any[] = [];
    let series: any = [
      {
        name: "act_id",
        type: "line",
        data: [],
      },
    ];
    arr.map((val: any[]) => {
      xArr.push(val[0] / 1000);
      series[0].data.push(val[1]);
    });
    return {
      tooltip: {},
      xAxis: {
        data: xArr,
      },

      yAxis: {
        min: -2,
        max: 5,
      },
      series: series,
    };
  }
  /**检测哪些分类有超过0.3的数据 */
  check_data() {
    this.dataArr.map((val: any) => {
      val.style["box-shadow"] = "";
      for (var i = 1; i < val.data.length; i++) {
        let data = val.data[i];
        for (let j = 1; j < data.length; j++) {
          if (data[j] > 0.3) {
            val.style["box-shadow"] = "2px 2px 2px #ce4e4e";
            // console.log(val.title,"标红");

            break;
          }
        }
      }
    });
    //console.log("this.dataArr", this.dataArr);
  }
  nav_click(i: number) {
    this.index = i;
    this.wsapi();
    for (var j = 0; j < this.dataArr.length; j++) {
      this.dataArr[j].style["border-color"] = "#868686";
    }
    this.dataArr[i].style["border-color"] = "rgb(226, 209, 55)";
  }

  wsapi() {
    let arr = this.dataArr[this.index].data.slice(this.times, this.times + 500);
    let xArr : any[] = [];
    let series: any[] = [];
    for (var i = 0; i < 4; i++) {
      series.push({
        name: "hs_" + i,
        type: "line",
        data: [],
      });
    }
    this.opt1 = {
      series : series,
      tooltip: {},
      xAxis: {
        data: [],
      },

      yAxis: {
        min: 0.3,
        max: 1,
      },
    };
 
    let ws = new WebSocket("ws://127.0.0.1:8888/ws/" + this.index);
    //window.console.log(456)
    ws.onmessage = (evt) => {
      window.console.log(evt.data);
      let res = JSON.parse(evt.data);
      for (var i = 0; i < res.data.length; i++) {
        this.opt1.series[i].data.push(res.data[i]);
        this.opt1.xAxis.data.push(res.simulink_time);
      }
      let myChart = this.$echarts.init(document.getElementById("myChart"));
      myChart.setOption(this.opt1);
    };
  }

  get_chart_data() {
    let arr = this.dataArr[this.index].data.slice(this.times, this.times + 100);
    let xArr: any[] = [];
    let series: any[] = [];
    arr.map((val: any) => {
      xArr.push((val[0] / 1000).toString());

      for (var i = 1; i < val.length; i++) {
        if (series.length < i) {
          series.push({
            name: "hs_" + i,
            type: "line",
            data: [],
          });
        }
        series[i - 1].data.push(val[i]);
      }
    });

    this.times++;
    return {
      tooltip: {},
      xAxis: {
        data: xArr,
      },

      yAxis: {
        min: 0.3,
        max: 1,
      },
      series: series,
    };
  }
}
</script>
<style scoped>
.main {
  display: flex;
  justify-content: center;
  align-items: center;
}
.cont-con {
  width: 1600px;
}
.title-con {
  padding: 1rem;
}
.title {
  border: 1px solid #868686;
  padding: 1rem;

  /* box-shadow: 2px 2px 2px #ce4e4e; */
}
</style>