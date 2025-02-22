<template>
  <div class="nous-chat-widget ns-absolute" :style="widgetStyle">
    <!-- chatbot toggle -->
    <div
      class="ns-fixed ns-bottom-5 ns-right-5 ns-w-12 ns-h-12 ns-flex ns-items-center ns-justify-center ns-rounded-full ns-shadow-lg ns-transition-all hover:ns-scale-110"
      role="button"
      @click="toggleChat"
      :style="{ backgroundColor: 'var(--nous-chat-color)' }"
    >
      <!-- open icon -->
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="24"
        height="24"
        fill="none"
        viewBox="0 0 24 24"
        v-if="isOpen"
      >
        <g id="tabler-icon-chevron-down__tabler-icon-chevron-down">
          <path
            id="tabler-icon-chevron-down__Vector"
            stroke="#fff"
            stroke-linecap="round"
            stroke-linejoin="round"
            stroke-width="2"
            d="m6 9 6 6 6-6"
          />
        </g>
      </svg>
      <!-- close icon -->
      <svg
        xmlns="http://www.w3.org/2000/svg"
        width="28"
        height="28"
        fill="none"
        viewBox="0 0 18 18"
        v-else
      >
        <g id="frame__Frame" clip-path="url(#frame__clip0_1212_3584)">
          <path
            id="frame__Vector"
            fill="#fff"
            d="M13.5 2.25a3 3 0 0 1 3 3v6a3 3 0 0 1-3 3H9.957l-3.572 2.143a.75.75 0 0 1-1.13-.558l-.005-.085v-1.5H4.5a3 3 0 0 1-2.996-2.85l-.004-.15v-6a3 3 0 0 1 3-3h9ZM10.5 9H6a.75.75 0 0 0 0 1.5h4.5a.75.75 0 1 0 0-1.5ZM12 6H6a.75.75 0 0 0 0 1.5h6A.75.75 0 1 0 12 6Z"
          />
        </g>
        <defs>
          <clipPath id="frame__clip0_1212_3584">
            <path fill="#fff" d="M0 0h18v18H0z" />
          </clipPath>
        </defs>
      </svg>
    </div>

    <!-- chatbot body container -->
    <Transition name="slide-fade">
      <div
        class="ns-fixed ns-bg-white ns-right-5 ns-bottom-[5.5rem] ns-rounded-2xl ns-shadow-chatbox ns-overflow-hidden"
        :style="{
          height: 'var(--nous-chat-height)',
          width: 'var(--nous-chat-width)',
          'z-index': 'var(--nous-chat-z-index)',
        }"
        v-if="isOpen"
      >
        <Transition name="fade" mode="out-in">
          <component
            :is="activeComponent"
            @open-recent-conversation="openRecentConversation"
            @go-back-home="handleGoBackHome"
          />
        </Transition>
      </div>
    </Transition>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, provide } from "vue";
import NousHome from "./NousHome.vue";
import NousConversation from "./NousConversation.vue";

// Props
const props = defineProps({
  webhookUrl: {
    type: String,
    required: true,
  },
  color: {
    type: String,
    default: "#ff2627",
  },
  title: {
    type: String,
    default: "Padi Bot",
  },
  firstMessage: {
    type: String,
    default: "Chat with us",
  },
  secondMessage: {
    type: String,
    default: "How can we help you today?",
  },
  initPayload: {
    type: String,
    default: "SpecialInitPayLoadDoNotTouch",
  },
  zIndex: {
    type: Number,
    default: 1000,
  },
  width: {
    type: String,
    default: "364px",
  },
  height: {
    type: String,
    default: "620px",
  },
  asrChunk: {
    type: Number,
    default: 800,
  },
  asrLanguage: {
    type: String,
    default: "none",
  },
  minimumSilentSecond: {
    type: Number,
    default: 1.5,
  },
});

// Provide all props to child components
provide("nousChatProps", props);

// State variables
const isOpen = ref(false);
const activeComponent = ref();

onMounted(() => {
  const userSessionId = localStorage.getItem("nous-user-session-id");
  if (userSessionId) {
    activeComponent.value = NousConversation;
  } else {
    activeComponent.value = NousHome;
  }
});

// Computed property for widget styles
const widgetStyle = computed(() => ({
  "--nous-chat-color": props.color,
  "--nous-chat-z-index": props.zIndex,
  "--nous-chat-width": props.width,
  "--nous-chat-height": props.height,
}));

// Functions
const toggleChat = () => {
  isOpen.value = !isOpen.value;
};

const openRecentConversation = () => {
  activeComponent.value = NousConversation;
};

// Handle go back home
const handleGoBackHome = () => {
  activeComponent.value = NousHome;
};
</script>
