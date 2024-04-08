<template>
  <div class="simonGameWrap">
    <div class="simonCircleWrap">
      <div
        v-for="sector in sectors"
        :key="sector.id"
        :class="{
          [sector.class]: true,
          sector_highlighted: sector.isHighlighted,
        }"
        @click="handleSectorClick(sector.id)"
      ></div>
    </div>
    <div class="infoAndControls">
      <p v-if="!isUserFail" class="roundNumber">
        {{ round }}: {{ roundNumber }}
      </p>
      <div @click="startTheGame">
        <StartButton
          v-if="!isGameOn"
          type="button"
          :label="startTheGameButtonLabel"
        />
      </div>
      <p v-if="isUserFail" class="lossAlert">
        {{ youLostAlert + roundNumber + " раунде" }}
      </p>
      <div class="setLevelButtons">
        <GameDifficulty
          :disabled="isHighlighting"
          :name="levelSelect.name"
          :buttons="levelSelect.buttons"
          :defaultValue="levelSelect.value"
          @update-input-data="setLevel"
        />
      </div>
    </div>
  </div>
</template>

<script>
import audio0 from "../assets/sounds/0.ogg";
import audio1 from "../assets/sounds/1.ogg";
import audio2 from "../assets/sounds/2.ogg";
import audio3 from "../assets/sounds/3.ogg";

import StartButton from "./StartButton.vue";
import GameDifficulty from "./GameDifficulty.vue";

export default {
  name: "SimonGame",
  components: {
    StartButton,
    GameDifficulty,
  },
  data() {
    return {
      roundNumber: 0,
      level: "easy",
      isGameOn: false,
      isUserFail: false,
      isHighlighting: false,
      audiosArray: [audio0, audio1, audio2, audio3],
      round: "Раунд",
      startTheGameButtonLabel: "Начать новую игру",
      youLostAlert: "Вы проиграли в ",
      levels: {
        easy: "легко",
        medium: "средне",
        hard: "сложно",
      },
      sectorsInit: [
        {
          id: 0,
          class: "sector sector_first",
          isHighlighted: false,
        },
        {
          id: 1,
          class: "sector sector_second",
          isHighlighted: false,
        },
        {
          id: 2,
          class: "sector sector_third",
          isHighlighted: false,
        },
        {
          id: 3,
          class: "sector sector_fourth",
          isHighlighted: false,
        },
      ],
    };
  },
  computed: {
    levelSelect() {
      return {
        name: "level",
        value: "easy",
        buttons: Object.entries(this.levels).map((level) => {
          const [name, nameLocale] = level;
          return {
            name: name,
            nameLocale: nameLocale,
          };
        }),
      };
    },
    timeout() {
      switch (this.level) {
        case "easy":
          return 1.5;
        case "medium":
          return 1;
        case "hard":
          return 0.4;
        default:
          return 1.5;
      }
    },
    sectors() {
      return this.sectorsInit.map((sector) => {
        return sector;
      });
    },
    gameSelectedSectorsArray() {
      const data = [];
      for (let i = 0; i < this.roundNumber; i++) {
        data.push(this.getRandomNumber());
      }
      return data;
    },
    userSelectedSectorsArray() {
      return [];
    },
  },
  methods: {
    playAudio(index) {
      const audio = new Audio(this.audiosArray[index]);
      audio.play();
    },
    getRandomNumber() {
      return Math.floor(Math.random() * this.sectorsInit.length);
    },
    setLevel(value) {
      this.clearUserSelectedSectors();
      this.level = value;
      if (this.isGameOn && !this.isHighlighting) this.highlightSectors();
    },
    setIsGameOn(value) {
      this.isGameOn = value;
    },
    startTheGame() {
      this.clearUserSelectedSectors();
      this.setIsGameOn(true);
      this.roundNumber = 1;
      this.isUserFail = false;
      this.highlightSectors();
    },
    startNextRound() {
      this.clearUserSelectedSectors();
      this.roundNumber++;
      this.highlightSectors();
    },
    setIsHighlighting(value) {
      this.isHighlighting = value;
    },
    highlightSector(value) {
      this.sectors.forEach((sector) => {
        if (sector.id === value) {
          this.sectors[sector.id].isHighlighted = true;
          this.playAudio(sector.id);
          setTimeout(() => {
            this.sectors[sector.id].isHighlighted = false;
          }, (this.timeout / 2) * 1000);
        } else this.sectors[sector.id].isHighlighted = false;
      });
    },
    highlightSectors() {
      this.setIsHighlighting(true);
      const { timeout, gameSelectedSectorsArray, highlightSector } = this;
      gameSelectedSectorsArray.forEach((number, index) => {
        setTimeout(() => {
          highlightSector(number);
        }, timeout * 1000 * (index + 1));
      });
      setTimeout(() => {
        this.setIsHighlighting(false);
      }, timeout * 1000 * (gameSelectedSectorsArray.length + 0.6));
    },
    setUserSelectedSectors(value) {
      this.userSelectedSectorsArray.push(value);
    },
    clearUserSelectedSectors() {
      this.userSelectedSectorsArray.length = 0;
    },
    setIsUserFail(userSelectedSectorId) {
      const {
        gameSelectedSectorsArray: gameSelectedArray,
        userSelectedSectorsArray: userSelectedArray,
      } = this;
      if (
        gameSelectedArray[userSelectedArray.length - 1] !== userSelectedSectorId
      ) {
        this.handleUserFail();
      }
    },
    handleUserFail() {
      this.isUserFail = true;
      this.setIsGameOn(false);
    },
    handleSectorClick(sectorId) {
      const {
        gameSelectedSectorsArray: gameSelectedArray,
        userSelectedSectorsArray: userSelectedArray,
        isHighlighting,
        isGameOn,
        playAudio,
        setUserSelectedSectors,
        setIsUserFail,
        startNextRound,
      } = this;
      if (isHighlighting) return;
      playAudio(sectorId);
      if (!isGameOn) return;
      setUserSelectedSectors(sectorId);
      setIsUserFail(sectorId);
      if (
        !this.isUserFail &&
        gameSelectedArray.length === userSelectedArray.length
      ) {
        startNextRound();
      }
    },
  },
};
</script>
<style scoped>
.simonGameWrap {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 40px;
}

.simonCircleWrap {
  min-width: 300px;
  max-width: 300px;
  max-height: 300px;
  min-height: 300px;
  border: 4px solid rgb(147, 146, 146);
  border-radius: 50%;
  position: relative;
  cursor: pointer;
}

.infoAndControls {
  display: flex;
  flex-direction: column;
  row-gap: 14px;
}

.sector {
  transition: 0.25s;
  position: absolute;
  border-radius: inherit;
  width: 100%;
  height: 100%;
  opacity: 0.7;
}

.sector_first {
  clip-path: polygon(50% 0%, 50% 50%, 0 50%, 0 0);
  background-color: #f8ee64;
}
.sector_second {
  clip-path: polygon(100% 0%, 100% 50%, 50% 50%, 50% 0%);
  background-color: dodgerblue;
}
.sector_third {
  clip-path: polygon(50% 50%, 50% 100%, 100% 100%, 100% 50%);
  background-color: #25db22;
}
.sector_fourth {
  clip-path: polygon(0 50%, 0 100%, 50% 100%, 50% 50%);
  background-color: #fc432e;
}
.sector_highlighted {
  opacity: 1 !important;
}
.sector:hover {
  opacity: 1;
}
</style>
