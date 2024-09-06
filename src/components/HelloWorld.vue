<template>
  <div class="carousel-container">
    <div
      class="carousel-wrapper"
      :style="{
        width: `${carouselItems.length * 100}vw`,
        transform: `translateX(${translateX}px)`,
        transition: isDragging
          ? 'none'
          : 'transform 0.8s ease, background-color 0.8s ease',
        backgroundColor: currentColor,
      }"
      @mousedown="handleMouseDown"
      @mousemove="handleMouseMove"
      @mouseup="handleMouseUp"
      @mouseleave="handleMouseUp"
    >
      <div
        v-for="(item, index) in carouselItems"
        :key="index"
        class="carousel-item"
        :style="{
          width: `${carouselWidth}px`,
          transform: `rotate(${isDragging ? rotateDegree : 0}deg)`,
          transition: isDragging ? 'none' : 'transform 0.8s ease',
        }"
      >
        <div class="content">
          <div class="pic-box">
            <img :src="item.pic" alt="" />
          </div>
          <div class="music-text">
            <h2>{{ item.title }}</h2>
            <audio
              ref="audio"
              @ended="onEnded"
              :src="item.url"
              @timeupdate="updateLyrics"
              controls
            ></audio>
            <div class="lyrics" ref="lyricsContainer">
              <p v-for="(line, index) in currentLyrics" :key="index">
                {{ line }}
              </p>
            </div>
            <p>{{ item.author }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from "vue";

const carouselItems = ref([
  {
    title: "徒花の涙",
    author: "ウォルピスカーター",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1431562483&auth=4450f7c31eda6e189bc913094e882517f7f1d14e",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951164811076014&auth=27bf98fe0f9c993918f7104e0bb12480dcdf5cf1",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1431562483&auth=9fe42adc281da4f9dedc1a0c9128b706ac672db6",
  },
  {
    title: "恋",
    author: "星野源",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1334405083&auth=4b3e46a7bb5b22610bbcc17a34d6decbcbfacb5f",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951164332819387&auth=b012b4e2769450a9a6250956fe9353cf293fe382",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1334405083&auth=10cd7f5ba1f8024b800a2e80ce0d340a6920e455",
  },
  {
    title: "Lucky",
    author: "Lenka",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=512377275&auth=8950cb3677f23221be7e261f2084a132b66a6a55",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951164939530636&auth=f0b57b99be8f7576f950ad4adfbaac015c85c40d",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=512377275&auth=613358333118c4149ad7384e5c81128fb7647b96",
  },
  {
    title: "Give Me Your Love",
    author: "Sigala / John Newman / Nile Rodgers",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1313089513&auth=4b981d592340076afe3bb6d9eda15cdc25906065",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951165983519139&auth=6979e4bbbf43bae4bfb41e5cba0c143444b6b20c",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1313089513&auth=ae977a60b5b390352d02343eb15d611ae28dd976",
  },
  {
    title: "fish in the pool・花屋敷",
    author: "ヘクとパスカル",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=31861269&auth=391152522f1308759d00bfc154e5c35eebeb3dde",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168763573429&auth=0db24060dd8e1cd8a9a0d6264f3c01f8c652e43b",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=31861269&auth=f7586333e80a46a3992831632925d815a6acfb9b",
  },
  {
    title: "Tall Ground",
    author: "Deluxe",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=28413830&auth=cd9ba0c0a258ae3c58ff097e4370638f9a632165",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951164429559022&auth=0c604d48567db1be34c6686cb4865b033c32303b",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=28413830&auth=88f32ea11ae582068ce662d915bfd498f31fdc7c",
  },
  {
    title: "ワレワレは宇宙人だ。",
    author: "Sooda",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2613061203&auth=961aeb9c2967ceb8549a3cdcfbf10d55950da1b9",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951169832751821&auth=39c0e24ef64f4421f08386393a61508f86d805b6",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2613061203&auth=c71702529d24d2bc6386c247b166383899fd0cb7",
  },
  {
    title: "Alice in 冷凍庫",
    author: "ウォルピスカーター",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2020611010&auth=3435890a71ef27a74b3b7cfbb795ccd99e33304d",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168292624420&auth=6a2b08d901ffa14d1a6198f1eaa964ee05752bba",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2020611010&auth=98e505e9d5f262909e0bd47bf7ddbe07d0e634fe",
  },
  {
    title: "p.h.",
    author: "水槽",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1498757522&auth=fae6357c23ec980a10f3cec2ea16de31999bd1ca",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951165502847414&auth=638afaa46ce12d88d318d8121e8ff02a271bb970",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1498757522&auth=2138d7358b3e9a310db40b7fe7c4d3dbf8874629",
  },
  {
    title: "扉をあけて",
    author: "ANZA",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=555959&auth=de51cdc46020442aa75c20ac64ccb692d9d1aa4f",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951167909729662&auth=98704b8d0dc98736538bd2763ef7bf3956f4d30b",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=555959&auth=d4cc89bd0aae82e2f529d0c915f24ffe2ca47617",
  },
  {
    title: "シリウスの心臓",
    author: "ヰ世界情緒",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1907751320&auth=2e71a01be04d813846dd21fd171714043e762e7a",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168567591834&auth=2c7f6ad1b64ffa9c71343dc54e190c30670815e6",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1907751320&auth=fdb839b35dfc5dd4eabd24fd89c7d0e3040931fe",
  },
  {
    title: "かいか",
    author: "廻花",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2146779700&auth=b414cf3ccaa787332bf0ba0c8b0626c0287eacfc",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951169505146864&auth=a212a4454eef4c0d7f61f9ce6c015a35406ce096",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2146779700&auth=fb864e465d0c84f8176ead91fa1ce70bfed6e379",
  },
  {
    title: "Life Will Change",
    author: "Lyn",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=454231736&auth=656802000f52026b79c2a77034bfe56903628cf1",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951165567176149&auth=9d09266dd8123023bd6ca9d7663121744b555f83",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=454231736&auth=c9379333401e3da5f81433e889f3673a9c7f07d2",
  },
  {
    title: "Baby you",
    author: "有華",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2008987398&auth=22577e02212ab6df9b31b3c4c351831aee972eaf",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168171131557&auth=61f9a0dc8d3a38c2597bd5ae4036525fee0e6309",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2008987398&auth=35b82e48e4f607522a7f87b2e14a92931d36749c",
  },
  {
    title: "それを世界と言うんだね",
    author: "花譜",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2026787178&auth=c38f45e5e946bde9f9a7b6a9f7b7ade287d1dcd3",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168436884742&auth=529367a003f5d4b439f9a7f07286deed4a4049dc",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2026787178&auth=f105653ee5982cf936aa594f8720d2e53ae84d42",
  },
  {
    title: "花 feat. 花譜",
    author: "Guiano / 花譜",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=2048604694&auth=55fc574c0cffb781c4a5e26250d6bfc5d56cc72e",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951168622465171&auth=221a7d4aff05e8456b3d5012a0610d4b512569a3",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=2048604694&auth=3dcf37c639477ae7f28e160d73f80f60d7352cd0",
  },
  {
    title: "心做し",
    author: "majiko",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=31421443&auth=1d2297d8e46b1d09e306f9aacc7beb62a3977d43",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951167814138971&auth=9382d7950424063ab7e28cbd7c343ad25a7a1980",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=31421443&auth=2c7baa7ba9b624f5e5fdfbc83e9fe4e97143d10d",
  },
  {
    title: "HACK",
    author: "末吉秀太",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=1460572578&auth=3b966e5b89762dd546f12c51e37ec71ab9db5d09",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=109951165114780620&auth=3245fb22fcda2130177ca3506b792f1a8e3096e1",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=1460572578&auth=e65e1ebdc134c4a53bf8c1df7aaf8cbcb488d5f7",
  },
  {
    title: "しろつめくさ",
    author: "ハンバート ハンバート",
    url: "https://api.i-meto.com/meting/api?server=netease&type=url&id=740765&auth=76819ddc800d7bc98e3c6999e994f76215c23d68",
    pic: "https://api.i-meto.com/meting/api?server=netease&type=pic&id=609129441799520&auth=c6ec76964183d4d04fecdc81d83999bde51c6443",
    lrc: "https://api.i-meto.com/meting/api?server=netease&type=lrc&id=740765&auth=ee11ad5395741c9d3140aaa8daf6910c4657c9bf",
  },
]);

// 颜色数组，为每张卡片定义一个颜色
const colors = [
  "#FF7F7F",
  "#7FB3FF",
  "#eea2a4",
  "#f43e06",
  "#f1ca17",
  "#10aec2",
  "#61649f",
  "#f9bd10",
  "#5a191b",
  "#55bb8a",
  "#fb8b05",
  "#f07c82",
  "#2e317c",
  "#f26b1f",
  "#5a1216",
  "#5dbe8a",
  "#0f59a4",
  "#fa5d19",
  "#bec936",
  "#82202b",
  "#93b5cf",
  "#ed4845",
  "#806d9e",
  "#2486b9",
];
let currentIndex = 0;
let isDragging = false;
let startX = 0;
let translateX = ref(0);
let currentColor = ref(colors[currentIndex]);
let carouselWidth = ref(window.innerWidth); // 使用 ref 使宽度响应变化
let endedListener = ref(null);

// 音乐与歌词相关变量
const audio = ref(null);
const lyricsContainer = ref(null);
let currentLyrics = ref([]);
let lyrics = {}; // 存储歌词及其时间戳
// 旋转角度，随拖动变化
let rotateDegree = ref(0);

const updateCarouselWidth = () => {
  carouselWidth.value = window.innerWidth;
};

onMounted(() => {
  window.addEventListener("resize", updateCarouselWidth);
  updateCarouselWidth();
  const audioElement = getAudioElement();
  if (audioElement && typeof audioElement.play === "function") {
    audioElement.src = carouselItems.value[currentIndex].url;
    audioElement.addEventListener("ended", endedListener); // 添加 ended 事件监听
  }
});

onUnmounted(() => {
  window.removeEventListener("resize", updateCarouselWidth);
  const audioElement = getAudioElement();
  if (audioElement) {
    audioElement.removeEventListener("ended", handleAudioEnded); // 移除 ended 事件监听
  }
});

const handleAudioEnded = () => {
  console.log("下一首");
  slideTo(currentIndex + 1); // 播放下一首
};

const handleMouseDown = (event) => {
  isDragging = true;
  startX = event.clientX;
};

const onEnded = () => {
  handleAudioEnded();
};

const handleMouseMove = (event) => {
  if (isDragging) {
    const diffX = event.clientX - startX;
    translateX.value = -currentIndex * carouselWidth.value + diffX;
    rotateDegree.value = (diffX / carouselWidth.value) * 45;
  }
};

const handleMouseUp = () => {
  if (isDragging) {
    const diffX = translateX.value + currentIndex * carouselWidth.value;
    const threshold = carouselWidth.value / 4;
    if (diffX > threshold) {
      slideTo(currentIndex - 1);
    } else if (diffX < -threshold) {
      slideTo(currentIndex + 1);
    } else {
      slideTo(currentIndex);
    }
    isDragging = false;
    rotateDegree.value = 0;
  }
};

const slideTo = (index) => {
  console.log(index, "index");
  const totalSlides = carouselItems.value.length;
  if (index < 0) {
    currentIndex = 0;
  } else if (index >= totalSlides) {
    currentIndex = totalSlides - 1;
  } else {
    currentIndex = index;
  }
  translateX.value = -currentIndex * carouselWidth.value;
  currentColor.value = colors[currentIndex];
  loadLyrics(); // 切换歌曲时加载对应的歌词
};

// 加载歌词文件
const loadLyrics = () => {
  const item = carouselItems.value[currentIndex];
  fetch(item.lrc)
    .then((response) => response.text())
    .then((data) => {
      const lines = data.split("\n");
      lyrics = lines.reduce((acc, line) => {
        const [time, text] = line.split("]");
        if (time && text) {
          const [minutes, seconds] = time
            .replace("[", "")
            .split(":")
            .map(Number);
          const timestamp = minutes * 60 + seconds;
          acc[timestamp] = text.trim();
        }
        return acc;
      }, {});
      currentLyrics.value = [];
    });
  updateLyrics();
};

// 获取最接近的时间戳
const findClosestTimestamp = (time) => {
  const timestamps = Object.keys(lyrics).map(Number);
  return timestamps.reduce((prev, curr) =>
    Math.abs(curr - time) < Math.abs(prev - time) ? curr : prev
  );
};

const getAudioElement = () => {
  if (Array.isArray(audio.value)) {
    return audio.value[currentIndex]; // 获取当前页面的audio实例
  }
  return audio.value;
};

// 根据歌曲进度更新歌词
const updateLyrics = () => {
  const audioElement = getAudioElement();
  if (audioElement) {
    audioElement.play();
    const currentTime = audioElement.currentTime;
    const timestamps = Object.keys(lyrics)
      .map(Number)
      .sort((a, b) => a - b);
    const nextLyricIndex = timestamps.findIndex((time) => time > currentTime);

    if (nextLyricIndex !== -1) {
      const closestTimestamp = timestamps[nextLyricIndex - 1];
      const nextTimestamp = timestamps[nextLyricIndex];

      // 显示当前歌词并隐藏上一句
      currentLyrics.value = [lyrics[closestTimestamp]];

      // 清除前一句歌词
      if (currentTime >= nextTimestamp) {
        currentLyrics.value = [];
      }
    }
  }
};

// 监听歌曲变化并加载新歌词
watch(
  () => carouselItems.value[currentIndex].url,
  () => {
    if (audio.value) {
      audio.value.src = carouselItems.value[currentIndex].url;
      audio.value.load(); // 确保音频已加载
      audio.value.play();
      loadLyrics();
    }
  }
);
</script>

<style scoped>
.carousel-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  position: relative;
  z-index: 9999;
}

.carousel-wrapper {
  display: flex;
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  transition: transform 0.8s ease, background-color 0.8s ease;
}

.carousel-item {
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

.content {
  width: 70%;
  display: flex;
  justify-content: space-between;
  text-align: center;
}

.pic-box {
  width: 300px;
  height: 300px;
  border: 1px solid #000;
  overflow: hidden;
  box-shadow: 10px 10px 4px rgba(0, 0, 0, 0.3);
}

.music-text {
  margin-right: 50px;
  margin-top: 60px;
}

h2 {
  font-size: 2rem;
  margin-bottom: 20px;
}

p {
  font-size: 1.2rem;
}

.lyrics {
  max-height: 300px;
  overflow-y: auto;
  padding: 1rem;
  text-align: left;
  border: 1px solid #ccc;
}
</style>