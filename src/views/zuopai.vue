<template>
  <div>
    <form action="">
      <b-form-radio-group v-model="radioChecked">
        <table align="center" border="1px" cellpadding="10px">
          <tr>
            <td colspan="3"><b>麻将做牌器</b> <span style="font-size:8px;">(v1.0)</span></td>
          </tr>
          <tr>
            <td>游戏：<br /><input v-model="gameId" @change="gameIdChange" /></td>
            <td>玩家：<br /><input v-model="playerId" /></td>
            <td>房间号：<b>{{ gameId }}</b><br /><input v-model="roomId" /></td>
          </tr>
          <tr>
            <td>
              <b-select name="select" v-model="addresSelected" :options="addresList">
              </b-select>
            </td>
            <td>
              <b-button block variant="outline-primary" size="lg" @click="addRoomCard">加卡</b-button>
            </td>
            <td>
              <b-button block variant="outline-primary" size="lg" @click="submitCard">提交做牌</b-button>
            </td>
          </tr>
          <tr>
            <td colspan="3">
              <ul>
                <li v-for="i in [1, 2, 3, 4, 5, 6]" :key="i">
                  <b-button v-for="card in cardList" :key="card.cardIndex" v-show="showCard(i, card.cardIndex)" @click="addCard($event, card.cardIndex)" class="cardBtn" size="lg" variant="outline-success">{{ card.text }}</b-button>
                </li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <b-button-group>
                <b-button variant="outline-danger" size="sm" @click="cleanCard('宝牌')"> 清除 </b-button>
                <b-button variant="outline-dark">
                  <b-radio name="pai" size="lg" value="宝牌">宝牌 ({{ submitInfo["宝牌"].length }}张)</b-radio>
                </b-button>
              </b-button-group>
            </td>
            <td id="宝牌" colspan="2">
              <b-button-group>
                <b-button v-for="(info, index) in submitInfo['宝牌']" :key="index" type="button" @click="delCard($event, index, info.value)" variant="outline-dark">{{ info.text }}</b-button>
              </b-button-group>
            </td>
          </tr>
          <tr>
            <td>
              <b-button-group>
                <b-button variant="outline-danger" size="sm" @click="cleanCard('混牌')"> 清除 </b-button>
                <b-button variant="outline-dark">
                  <b-radio name="pai" size="lg" value="混牌">混牌 ({{ submitInfo["混牌"].length }}张)</b-radio>
                </b-button>
              </b-button-group>
            </td>
            <td id="混牌" colspan="2">
              <b-button-group>
                <b-button v-for="(info, index) in submitInfo['混牌']" :key="index" type="button" @click="delCard($event, index, info.value)" variant="outline-dark">{{ info.text }}</b-button>
              </b-button-group>
            </td>
          </tr>
          <tr>
            <td>
              <b-button-group>
                <b-button variant="outline-danger" size="sm" @click="cleanCard('庄牌')"> 清除 </b-button>
                <b-button variant="outline-dark">
                  <b-radio name="pai" size="lg" value="庄牌">庄牌 ({{ submitInfo["庄牌"].length }}张)</b-radio>
                </b-button>
              </b-button-group>
            </td>
            <td id="庄牌" colspan="2">
              <b-button-group>
                <b-button v-for="(info, index) in submitInfo['庄牌']" :key="index" type="button" @click="delCard($event, index, info.value)" variant="outline-dark">{{ info.text }}</b-button>
              </b-button-group>
            </td>
          </tr>
          <tr>
            <td>
              <b-button-group>
                <b-button variant="outline-danger" size="sm" @click="cleanCard('手牌')"> 清除 </b-button>
                <b-button variant="outline-dark">
                  <b-radio name="pai" size="lg" value="手牌">手牌 ({{ submitInfo["手牌"].length }}张)</b-radio>
                </b-button>
              </b-button-group>
            </td>
            <td id="手牌" colspan="2">
              <b-button-group>
                <b-button v-for="(info, index) in submitInfo['手牌']" :key="index" type="button" @click="delCard($event, index, info.value)" variant="outline-dark">{{ info.text }}</b-button>
              </b-button-group>
            </td>
          </tr>
          <tr>
            <td>
              <b-button-group>
                <b-button variant="outline-danger" size="sm" @click="cleanCard('堆牌')"> 清除 </b-button>
                <b-button variant="outline-dark">
                  <b-radio name="pai" size="lg" value="堆牌">堆牌 ({{ submitInfo["堆牌"].length }}张)</b-radio>
                </b-button>
              </b-button-group>
            </td>
            <td id="堆牌" colspan="2">
              <b-button-group>
                <b-button v-for="(info, index) in submitInfo['堆牌']" :key="index" type="button" @click="delCard($event, index, info.value)" variant="outline-dark">{{ info.text }}</b-button>
              </b-button-group>
            </td>
          </tr>
        </table>
      </b-form-radio-group>
    </form>
  </div>
</template>

<script lang="ts">
import Vue from "vue";

export default Vue.extend({
  components: {},
  data() {
    return {
      gameId: "",
      playerId: "",
      roomId: "",
      radioChecked: "手牌",
      addresSelected: "https://lyqptesttwo.bflyzx.com:10416",
      addresList: [
        { value: "https://lyqptestthree.bflyzx.com:10416", text: "扑克组" },
        { value: "https://lyqptesttwo.bflyzx.com:10416", text: "二组" },
        { value: "https://lyqptestone.bflyzx.com:10416", text: "一组" },
        { value: "https://laoyoutest.bflyzx.com:10416", text: "杭州" },
        { value: "https://laoyoutestunion.bflyzx.com:10416", text: "联合" },
        { value: "http://127.0.0.1:10416", text: "本地" },
        { value: "http://192.168.146.168:10416", text: "郑哲" }
      ],
      get cardList() {
        const totalList: { cardIndex: string; text: string }[] = [];
        const cardInfo: any = {
          1: ["一万", "二万", "三万", "四万", "五万", "六万", "七万", "八万", "九万"],
          2: ["一条", "二条", "三条", "四条", "五条", "六条", "七条", "八条", "九条"],
          3: ["一饼", "二饼", "三饼", "四饼", "五饼", "六饼", "七饼", "八饼", "九饼"],
          4: ["红中", "发财", "白板"],
          5: ["东风", "南风", "西风", "北风"],
          6: ["春", "夏", "秋", "冬", "梅", "兰", "竹", "菊", "白坨"]
        };
        for (const key in cardInfo) {
          for (let i = 0; i < cardInfo[key].length; i++) {
            totalList.push({
              cardIndex: key + (i + 1),
              text: cardInfo[key][i]
            });
          }
        }
        return totalList;
      },
      submitInfo: { 宝牌: [], 混牌: [], 庄牌: [], 手牌: [], 堆牌: [] }
    };
  },
  methods: {
    gameIdChange(event: any) {
      console.log(event);
    },
    showCard(_i: number, _index: number): boolean {
      return Math.floor(_index / 10) == _i;
    },
    addRoomCard() {
      const _data = {
        "game_id": this.gameId,
        "uid": this.playerId,
        "action": "add_card",
        "cardnum": 1000
      };

      this.HTTPRequest(_data, function (e: XMLHttpRequest) {
        console.log("加卡成功");
      });
    },
    submitCard() {
      const _submitInfo: any = this.submitInfo as { 宝牌: number[], 混牌: number[], 庄牌: number[], 手牌: number[], 堆牌: number[] };
      const _cardNumList: any = { 宝牌: [], 混牌: [], 庄牌: [], 手牌: [], 堆牌: [] };
      for (const i in _submitInfo) {
        const _item: number[] = [];
        for (let j = 0; j < _submitInfo[i].length; j++) {
          _item.push(Number(_submitInfo[i][j].value));
        }
        _cardNumList[i] = _item;
      }
      const _data: {
        shou_pai: number[];
        zhuangpai: number[];
        dui_pai: number[];
        bao_pai: number[];
        hun_pai: number[];
        game_id: string;
        uid: string;
        room_id: string;
        action: "alter_zuo_pai";
      } = {
        "shou_pai": _cardNumList["手牌"],
        "zhuangpai": _cardNumList["庄牌"][0],
        "dui_pai": _cardNumList["堆牌"],
        "bao_pai": _cardNumList["宝牌"],
        "hun_pai": _cardNumList["混牌"],
        "game_id": this.gameId,
        "uid": this.playerId,
        "room_id": this.gameId + this.roomId,
        "action": "alter_zuo_pai"
      };

      this.HTTPRequest(_data, function (e: XMLHttpRequest) {
        console.log("做牌成功");
      });
    },
    addCard(e: MouseEvent, num: number) {
      // 当前点击的button
      const _curElement = e.target as HTMLElement;
      // 获取当前选中的 radio 按钮
      const _element = document.getElementById(`${this.radioChecked}`);
      // 要提交的牌型
      const _submitInfo: any = this.submitInfo;

      if (_element instanceof HTMLElement) {
        const _infoArr: { value: number; text: any }[] =
          _submitInfo[_element.id];
        const _item: { value: number; text: any } = {
          value: num,
          text: _curElement.textContent
        };
        // 当前的牌选择超过 4 张   则不加入手牌内
        if (_infoArr.filter(v => { return v.value == num; }).length < 4) {
          _infoArr.push(_item);
          // _infoArr.sort((a, b) => {
          //   return a.value - b.value;
          // });
        }
      }
    },
    delCard(e: MouseEvent, index: number, num: number) {
      // 获取当前选中的 radio 按钮
      const _element = document.getElementById(`${this.radioChecked}`);
      // 要提交的牌型
      const _submitInfo: any = this.submitInfo;

      if (_element instanceof HTMLElement) {
        _submitInfo[_element.id].splice(index, 1);
      }
    },
    cleanCard(value: string) {
      // 要提交的牌型
      const _submitInfo: any = this.submitInfo;
      _submitInfo[value].splice(0, _submitInfo[value].length);
    },
    HTTPRequest(_data: any, _callback?: Function) {
      const _request = new XMLHttpRequest();
      _request.onreadystatechange = function () {
        if (_request.readyState == 4 && _request.status == 200) {
          if (_callback) {
            _callback(_request);
          }
        }
      };
      _request.open("post", this.addresSelected);
      _request.setRequestHeader(
        "Content-Type",
        "application/x-www-form-urlencoded"
      );
      _request.send(JSON.stringify(_data));
    }
  }
});
</script>

<style scoped>
.cardBtn {
  margin: 0 5px;
}
#宝牌,
#混牌,
#庄牌,
#手牌,
#堆牌 {
  text-align: left;
}
ul {
  padding: 0;
}
li {
  list-style-type: none;
  margin: 10px 0;
}
</style>
