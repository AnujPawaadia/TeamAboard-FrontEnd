<template>
  <div class="chat-container">
    <!-- Sidebar -->
    <chat-sidebar :chats="chats" :existing-contacts="existingContacts" @select-chat="selectChat"
      @create-group="handleCreateGroup" />

    <!-- Chat Window -->
    <chat-window v-if="selectedChat" :selected-chat="selectedChat" :messages="messages" :current-user="currentUser"
      @send-message="sendMessage" />
    <div v-else class="chat-window-placeholder">
      <p>Select a chat to start messaging</p>
    </div>
  </div>
</template>

<script>
import { getRandomImage } from '@/Utils/randomImages'; // Import the utility
import { computed, ref, watch } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import ChatSidebar from '../components/ChatSidebar.vue';
import ChatWindow from '../components/ChatWindow.vue';

export default {
  name: 'ChatView',
  components: { ChatSidebar, ChatWindow },
  setup() {
    const router = useRouter();
    const route = useRoute();
    const currentUser = 'You'; // Static user for now
    const currentUserId = 'user-you'; // Mock ID for the current user

    const chats = ref([
      {
        id: 'chat1',
        name: 'Sarah Johnson',
        avatar: getRandomImage(),
        type: 'individual',
        lastMessage: 'Can you review my project proposal?',
        lastMessageTime: '09:15',
        messages: [
          { id: 1, sender: 'Sarah', text: 'Hey, I’m working on a web dev project proposal. Can you review it?', time: '09:10' },
          { id: 2, sender: 'You', text: 'Sure, send it over! I’ll check it by noon.', time: '09:15' },
        ],
      },
      {
        id: 'chat2',
        name: 'AI Research Group',
        avatar: getRandomImage(),
        type: 'group',
        members: ['user-sarah', 'user-mike', 'user-you'],
        lastMessage: 'Let’s schedule the ML model demo for Friday.',
        lastMessageTime: '14:30',
        messages: [
          { id: 1, sender: 'Mike', text: 'The neural network training is complete. Results look promising!', time: '14:20' },
          { id: 2, sender: 'You', text: 'Great! Let’s schedule the demo for Friday.', time: '14:30' },
        ],
      },
      {
        id: 'chat3',
        name: 'Michael Chen',
        avatar: getRandomImage(),
        type: 'individual',
        lastMessage: 'Found a great internship opportunity!',
        lastMessageTime: '11:45',
        messages: [
          { id: 1, sender: 'Michael', text: 'Found a great internship opportunity at a startup. Interested?', time: '11:40' },
          { id: 2, sender: 'You', text: 'Yes, share the details!', time: '11:45' },
          { id: 3, sender: 'Michael', text: 'It’s for a mobile app dev role. I’ll send the link.', time: '11:46' },
        ],
      },
      {
        id: 'chat4',
        name: 'Hackathon Team',
        avatar: getRandomImage(),
        type: 'group',
        members: ['user-emma', 'user-liam', 'user-you'],
        lastMessage: 'We need to finalize the UI design by tomorrow.',
        lastMessageTime: '16:20',
        messages: [
          { id: 1, sender: 'Emma', text: 'I’ve uploaded the wireframes to Figma.', time: '16:15' },
          { id: 2, sender: 'Liam', text: 'Looks good, but we need to tweak the color scheme.', time: '16:18' },
          { id: 3, sender: 'You', text: 'Agreed, let’s finalize the UI by tomorrow.', time: '16:20' },
        ],
      },
    ]);

    // Compute existing contacts from individual chats
    const existingContacts = computed(() => {
      return chats.value
        .filter(chat => chat.type === 'individual')
        .map(chat => ({
          id: chat.id,
          name: chat.name,
        }));
    });

    const messages = ref([]);
    const selectedChat = computed(() => {
      const chatId = route.params.id;
      return chats.value.find((chat) => chat.id === chatId) || null;
    });

    watch(
      () => route.params.id,
      (newId) => {
        const chatId = newId;
        const chat = chats.value.find((chat) => chat.id === chatId);
        messages.value = chat ? chat.messages : [];
      },
      { immediate: true }
    );

    const selectChat = (chatId) => {
      router.push(`/chat/${chatId}`);
    };

    const sendMessage = (text) => {
      if (text.trim() && selectedChat.value) {
        const newMessage = {
          id: Date.now(),
          sender: currentUser,
          text: text.trim(),
          time: new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }),
        };
        selectedChat.value.messages.push(newMessage);
        messages.value = selectedChat.value.messages;
        selectedChat.value.lastMessage = newMessage.text;
        selectedChat.value.lastMessageTime = newMessage.time;
      }
    };

    const handleCreateGroup = (newGroup) => {
      // Add the current user to the group members
      newGroup.members.push(currentUserId);
      // Assign a random avatar to the new group using getRandomImage
      newGroup.avatar = getRandomImage();
      chats.value.push(newGroup);
    };

    return { chats, selectedChat, messages, currentUser, existingContacts, selectChat, sendMessage, handleCreateGroup };
  },
};
</script>

<style scoped>
.chat-container {
  display: flex;
  height: 100vh;
  width: 100%;
  color: #000000;
}

.chat-window-placeholder {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.chat-window-placeholder p {
  color: #000000;
  font-size: 1.2rem;
}

:deep(::-webkit-scrollbar) {
  width: 8px;
}

:deep(::-webkit-scrollbar-track) {
  background: #72cba4;
  border-radius: 10px;
}

:deep(::-webkit-scrollbar-thumb) {
  background-color: #72cba4;
  border-radius: 10px;
  border: 2px solid #72cba4;
}

:deep(::-webkit-scrollbar-thumb:hover) {
  background-color: #5bb688;
}

:deep(::-webkit-scrollbar-thumb:active) {
  background-color: #3fa574;
}

:deep(::-webkit-scrollbar-thumb:vertical) {
  background-color: #72cba4;
}
</style>
