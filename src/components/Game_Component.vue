<template>
  <div class="container">
    <h2>Уровень {{count}}</h2>
    <button @click="startGame">Начать</button>
    <div class="notification" v-if="notification">
      <p>{{result}}</p>
    </div>
    <div class="radio" v-for="(item,i) in complexityArr" :key="i">
      <input type="radio" :id="item.id" :value="item.value" v-model="complexityModel" />
      <label :for="item.id">{{item.name}}</label>
    </div>
    <div class="audio">
      <audio class="audio1" controls>
        <source src="../sound/1.mp3" type="audio/ogg" />
      </audio>
      <audio class="audio2" controls>
        <source src="../sound/2.mp3" type="audio/ogg" />
      </audio>
      <audio class="audio3" controls>
        <source src="../sound/3.mp3" type="audio/ogg" />
      </audio>
      <audio class="audio4" controls>
        <source src="../sound/4.mp3" type="audio/mp3" />
      </audio>
    </div>
    <div class="group-button pointEventer">
      <div
        class="group-button__1"
        data-id="1"
        @click="sound('group-button__1', 'audio1'),addClick($event)"
      ></div>
      <div
        class="group-button__2"
        data-id="2"
        @click="sound('group-button__2', 'audio2'),addClick($event)"
      ></div>
      <div
        class="group-button__3"
        data-id="3"
        @click="sound('group-button__3', 'audio3'),addClick($event)"
      ></div>
      <div
        class="group-button__4"
        data-id="4"
        @click="sound('group-button__4', 'audio4'),addClick($event)"
      ></div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      notification: false,
      result: null,
      complexity: "",
      complexityModel: 1500,
      complexityArr: [
        {
          name: "Простой уровень",
          value: "1500",
          id: "easy"
        },
        {
          name: "Средний уровень",
          value: "1000",
          id: "medium"
        },
        {
          name: "Тяжелый уровень",
          value: "400",
          id: "hard"
        }
      ],
      soundArr: [],
      count: 1,
      clickArr: [],
      activeButton: [],
      activeGame: false
    };
  },
  methods: {
    //МОЖНО СДЕЛАТЬ НАТИВНЫМИ СПОСОБАМИ VUE но так как мы не можем передать в функцию REF, то кода получается больне, не ок
    sound(el1, el2) {
      this.clear();
      let $el1 = document.querySelector(`.${el1}`);
      let $el2 = document.querySelector(`.${el2}`);

      $el1.classList.add("active-button");
      setTimeout(() => {
        $el2.play();
      }, 10);

      this.activeButton.push($el1);

      setTimeout(() => {
        $el1.classList.remove("active-button");
      }, this.complexityModel - 10);
    },
    clear(e) {
      this.activeButton.forEach(element => {
        element.classList.remove("active-button");
      });
    },
    startGame() {
      this.count = 1;
      this.soundArr = [];
      this.clickArr = [];

      this.random();
    },
    addClick(e) {
      this.clickArr.push(e.target.dataset.id);
      if (Number(this.soundArr.length) == Number(this.clickArr.length)) {
        if (this.soundArr.join() == this.clickArr.join()) {
          document.querySelector(".group-button").classList.add("pointEventer");
          setTimeout(() => {
            this.nextLvl();
            this.random();
          }, 1000);
          this.result = "Вы молодец";
          this.openNotific();
        } else {
          document.querySelector(".group-button").classList.add("pointEventer");
          this.count = 1;
          this.result = "Попробуйте еще раз";
          this.openNotific();
        }
      } else {
        for (let i = 0; i <= this.clickArr; i++) {
          console.log(this.clickArr[i] != this.soundArr[i]);
          if (this.clickArr[i] != this.soundArr[i]) {
            document
              .querySelector(".group-button")
              .classList.add("pointEventer");
            this.count = 1;
            this.result = "Попробуйте еще раз";
            this.openNotific();
          } else {
            break;
          }
        }
      }
    },
    nextLvl() {
      this.count++;
      this.soundArr = [];
      this.clickArr = [];
    },
    randonRez() {
      return Math.floor(Math.random() * (5 - 1) + 1);
    },
    random() {
      for (let i = 1; i <= this.count; i++) {
        let randomNum = this.randonRez();
        if (
          (this.soundArr[this.soundArr.length - 1] == randomNum) &
          (randomNum > 2)
        ) {
          randomNum--;
        } else if (
          (this.soundArr[this.soundArr.length - 1] == randomNum) &
          (randomNum < 4)
        ) {
          randomNum++;
        }
        this.soundArr.push(randomNum);
      }
      for (const [i, item] of this.soundArr.entries()) {
        setTimeout(() => {
          this.promiceCall(item, i);
        });
      }
    },
    promiceCall(item, i) {
      new Promise((resolve, reject) => {
        setTimeout(() => {
          if (this.sound.length != 0) {
            this.sound(`group-button__${item}`, `audio${item}`);
          }
        }, this.complexityModel * i);

        setTimeout(() => {
          document
            .querySelector(".group-button")
            .classList.remove("pointEventer");
        }, this.complexityModel * this.soundArr.length);
      });
    },
    openNotific() {
      this.notification = true;

      setInterval(() => {
        this.notification = false;
      }, 3000);
    }
  }
};
</script>
<style lang="scss" scoped>
.audio {
  display: none;
}
.pointEventer {
  pointer-events: none !important;
}
.container {
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  background: url("../sound/bgImg.png");
}
button {
  width: 5em;
  box-sizing: border-box;
  font-size: 1.4em;
  -webkit-border-radius: 10px 10px 10px 10px;
  border-radius: 10px 10px 10px 10px;
  background: #6dabe8;
  border: none;
  padding: 0.3em 0.6em;
  &:active {
    background: #88b8eb;
  }
  &:focus {
    outline: none;
  }
}
.radio {
  margin: 5px;
}
.group-button {
  width: 250px;
  height: 250px;
  border-radius: 50%;
  background-color: yellowgreen;
  position: relative;
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  &__1,
  &__2,
  &__3,
  &__4 {
    width: 49%;
    height: 49%;
    padding: 1px;
  }
  &__1 {
    border-radius: 100% 0 0 0;
    background-color: #d8eb73;
  }
  &__2 {
    border-radius: 0 100% 0 0;
    background-color: #fef585;
  }
  &__3 {
    border-radius: 0 0 0 100%;
    background-color: #ff9a8e;
  }
  &__4 {
    border-radius: 0 0 100% 0;
    background-color: #78bcff;
  }
}
.active-button {
  background-color: red;
}

.notification {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  top: 10%;
  left: 50%;
  width: 150px;
  height: 40px;
  border-radius: 15px;
  transform: translateX(-50%);
  background-color: white;
  -webkit-box-shadow: 0px 0px 19px 5px rgba(0, 0, 0, 0.32);
  box-shadow: 0px 0px 19px 5px rgba(0, 0, 0, 0.32);
}
</style>