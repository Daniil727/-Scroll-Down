<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          Quasar App
        </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >
      <q-list>
        <q-item-label
          header
        >
          Essential Links
        </q-item-label>

        <EssentialLink
          v-for="link in linksList"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>

   <!-- Чат -->
    <q-btn
      @click="botGreeting"
      class="icon-chat bg-primary"
      icon="chat"
      style="color: white"
    ></q-btn>

    <!-- popup чата -->
    <q-dialog v-model="medium">
      <q-card class="popup-chat-box">
        <q-card-section class="chat-header q-mt-xs">
          <p class="text-h5 q-ml-lg">
            <span class="text-h4 text-primary q-mr-md">FANSYMAG</span>Поддержка
            FANSYMAG
          </p>
        </q-card-section>

        <q-card-section>
          <div class="scrollContainer" ref="scrollContainer">
            <q-chat-message
              class="q-ml-lg q-mr-lg"
              v-for="(message, index) in chat"
              :key="index"
              :name="message.name"
              :text="message.text"
              :stamp="message.stamp"
              :sent="message.sent"
            />
          </div>
        </q-card-section>

        <q-card-section class="chat-footer row justify-between">
          <q-input
            class="col-9 q-ml-lg"
            @keyup.enter="chatPush"
            outlined
            v-model="textChat"
            label="Ваше сообщение..."
          />
          <div class="col-1 q-mr-xl">
            <q-btn
              @click="chatPush"
              size="lg"
              push
              color="primary"
              round
              icon="chat"
            />
          </div>
        </q-card-section>
      </q-card>
    </q-dialog>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'
import { date } from "quasar";

const scrollContainer = ref(null);
const textChat = ref([""]);
const chat = ref([]);
const medium = ref(false);

defineOptions({
  name: 'MainLayout'
})

// ВАРИАНТ С КОТОРЫМ ХОРОШО ОТРАБАТЫВАЕТ ПРЫЖОК СКРОЛЛА

function botGreeting() {
  medium.value = true;
  if (chat.value.length === 0) {
    setTimeout(() => {
      let timeStamp = Date.now();
      let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
      chat.value.push({
        name: "Ваш покорный слуга ",
        text: ["Чем я могу вам помочь?"],
        stamp: formattedString,
        sent: false,
      });
    },1000);
  }
}

function chatPush() {
  if (textChat.value !== "") {
    let timeStamp = Date.now();
      let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
    chat.value.push({
      name: "пользователь",
      text: [textChat.value],
      stamp: formattedString,
      sent: true,
    });
    setTimeout(() => {
      if (scrollContainer.value) {
        scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;
      }
    }, 0);
    setTimeout(() => {
      let timeStamp = Date.now();
      let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
      chat.value.push({
        name: "Ваш покорный слуга ",
        text: [
          "Обратитесь по вашему вопросу на горячую линию по номеру телефона: 8-800-555-35-35",
        ],
        stamp: formattedString,
        sent: false,
      });
    }, 1000);
    setTimeout(() => {
      if (scrollContainer.value) {
        scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;
      }
    }, 1001);
    textChat.value = "";
  }
}


// ВАРИАНТ ЧЕРЕЗ watch, ТУТ СТРАННАЯ ОТРАБОТКА ( НЕ ПРЫГАЕТ ДО КОНЦА ВНИЗ, 1 ПОСЛЕДНЕЕ СООБЩЕНИЕ НЕ ВИДНО )

// function botGreeting() {
//     medium.value = true;
//     if (chat.value.length === 0) {
//       setTimeout(() => {
//         let timeStamp = Date.now();
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//         chat.value.push({
//           name: "Ваш покорный слуга ",
//           text: ["Чем я могу вам помочь?"],
//           stamp: formattedString,
//           sent: false,
//         });
//       },1000);
//     }
//   }

//   function chatPush() {
//     if (textChat.value !== "") {
//       let timeStamp = Date.now();
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//       chat.value.push({
//         name: "пользователь",
//         text: [textChat.value],
//         stamp: formattedString,
//         sent: true,
//       });

//       setTimeout(() => {
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//         chat.value.push({
//           name: "Ваш покорный слуга ",
//           text: [
//             "Обратитесь по вашему вопросу на горячую линию по номеру телефона: 8-800-555-35-35",
//           ],
//           stamp: formattedString,
//           sent: false,
//         });
//       }, 1000);
      
//       textChat.value = "";
//     }
//   }
// watch(chat.value, async() =>{
//         if (scrollContainer.value) {
//           scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;
//         }
//       })

// ЕЩЕ ВАРИАНТ, ПЫТАЛСЯ ДЕЛАТЬ ЧЕРЕЗ ФУНКЦИЮ (ТОЖЕ НЕ ПРЫГАЕТ ДО КОНЦА ВНИЗ, ТОЛЬКО УЖЕ 2 ПОСЛЕДНИХ СООБЩЕНИЯ НЕ ВИДНО)

// function scrollDown(){
//   if (scrollContainer.value) {
//     scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;
// }
// }
  
//   function botGreeting() {
//     medium.value = true;
//     if (chat.value.length === 0) {
//       setTimeout(() => {
//         let timeStamp = Date.now();
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//         chat.value.push({
//           name: "Ваш покорный слуга ",
//           text: ["Чем я могу вам помочь?"],
//           stamp: formattedString,
//           sent: false,
//         });
//       },1000);
//     }
//   }

//   function chatPush() {
//     if (textChat.value !== "") {
//       let timeStamp = Date.now();
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//       chat.value.push({
//         name: "пользователь",
//         text: [textChat.value],
//         stamp: formattedString,
//         sent: true,
//       });
//       scrollDown(()=>{
//         console.log("скролл вниз")
//       })

//       setTimeout(() => {
//         let formattedString = date.formatDate(timeStamp, 'HH:mm:ss')
//         chat.value.push({
//           name: "Ваш покорный слуга ",
//           text: [
//             "Обратитесь по вашему вопросу на горячую линию по номеру телефона: 8-800-555-35-35",
//           ],
//           stamp: formattedString,
//           sent: false,
//         });
//       }, 1000);
//       scrollDown(()=>{
//         console.log("скролл вниз")
//       })

//       textChat.value = "";
//     }
//   }

// НУ И ЕСЛИ ОПУСТИТЬ setTimeout, ТО ТОЖЕ 2 ПОСЛЕДНИХ СООБЩЕНИЯ НЕ ВИДНО

// function botGreeting() {
//   medium.value = true;
//   if (chat.value.length === 0) {
//     setTimeout(() => {
//       let timeStamp = Date.now();
//       let formattedString = date.formatDate(timeStamp, "HH:mm:ss");
//       chat.value.push({
//         name: "Ваш покорный слуга ",
//         text: ["Чем я могу вам помочь?"],
//         stamp: formattedString,
//         sent: false,
//       });
//     }, 1000);
//   }
// }

// function chatPush() {
//   if (textChat.value !== "") {
//     let timeStamp = Date.now();
//     let formattedString = date.formatDate(timeStamp, "HH:mm:ss");
//     chat.value.push({
//       name: "пользователь",
//       text: [textChat.value],
//       stamp: formattedString,
//       sent: true,
//     });

//     if (scrollContainer.value) {
//       scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;

//       setTimeout(() => {
//         let formattedString = date.formatDate(timeStamp, "HH:mm:ss");
//         chat.value.push({
//           name: "Ваш покорный слуга ",
//           text: [
//             "Обратитесь по вашему вопросу на горячую линию по номеру телефона: 8-800-555-35-35",
//           ],
//           stamp: formattedString,
//           sent: false,
//         });
//       }, 1000);
//       if (scrollContainer.value) {
//         scrollContainer.value.scrollTop = scrollContainer.value.scrollHeight;

//         textChat.value = "";
//       }
//     }
//   }
// }



const linksList = [
  {
    title: 'Docs',
    caption: 'quasar.dev',
    icon: 'school',
    link: 'https://quasar.dev'
  },
  {
    title: 'Github',
    caption: 'github.com/quasarframework',
    icon: 'code',
    link: 'https://github.com/quasarframework'
  },
  {
    title: 'Discord Chat Channel',
    caption: 'chat.quasar.dev',
    icon: 'chat',
    link: 'https://chat.quasar.dev'
  },
  {
    title: 'Forum',
    caption: 'forum.quasar.dev',
    icon: 'record_voice_over',
    link: 'https://forum.quasar.dev'
  },
  {
    title: 'Twitter',
    caption: '@quasarframework',
    icon: 'rss_feed',
    link: 'https://twitter.quasar.dev'
  },
  {
    title: 'Facebook',
    caption: '@QuasarFramework',
    icon: 'public',
    link: 'https://facebook.quasar.dev'
  },
  {
    title: 'Quasar Awesome',
    caption: 'Community Quasar projects',
    icon: 'favorite',
    link: 'https://awesome.quasar.dev'
  }
]

const leftDrawerOpen = ref(false)

function toggleLeftDrawer () {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>

<style scoped lang="scss">
//чат popup
.icon-chat {
  position: fixed;
  right: 2%;
  top: 90%;
  z-index: 500;
  width: 50px;
  height: 50px;
  border-radius: 50%;
}
.q-dialog__inner--minimized > div {
  max-width: 800px;
}
.popup-chat-box {
  position: relative;
  width: 100%;
  height: 80%;
  border-radius: 50px;
}
.chat-card-test {
  width: 100%;
  height: 100%;
}
.chat-header {
  // position: absolute;
  // bottom: 0;
  border-bottom: solid 2px;
  border-color: $secondary;
}
.chat-footer {
  position: absolute;
  bottom: 2%;
  left: 0;
  width: 100%;
  border-top: solid 2px;
  border-color: $secondary;
}
.scrollContainer {
  width: 100%;
  max-height: calc(74vh - 200px);
  overflow-y: auto;
  pointer-events: all;
  margin-left: auto;
  margin-right: auto;
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.scrollContainer::-webkit-scrollbar {
  width: 0;
  height: 0;
}
.seach-popup {
  position: absolute;
  left: 0;
  top: 80px;
  padding: 12px;
  width: 100%;
  border-radius: 20px;
  z-index: 1000;
}
.scroll-popup-search {
  max-height: calc(100vh - 150px);
  overflow-y: auto;
  pointer-events: all;
  -ms-overflow-style: none;
  scrollbar-width: none;
}
.scroll-popup-search::-webkit-scrollbar {
  width: 0;
  height: 0;
}
</style>
