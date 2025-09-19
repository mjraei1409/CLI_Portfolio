<template>
  <div class="flex flex-col items-center justify-center min-h-screen bg-gray-900 text-gray-300 p-4 font-mono">
    <div class="window-container w-full max-w-4xl bg-gray-800 rounded-xl shadow-2xl overflow-hidden border border-gray-700">
      <!-- Window Header -->
      <div class="window-header flex items-center p-3 border-b border-gray-700">
        <div class="flex space-x-2">
          <span class="w-3 h-3 rounded-full bg-red-500"></span>
          <span class="w-3 h-3 rounded-full bg-yellow-500"></span>
          <span class="w-3 h-3 rounded-full bg-green-500"></span>
        </div>
        <div class="flex-grow text-center text-gray-300 text-sm font-bold">
          mjraei@portfolio:~
        </div>
      </div>

      <!-- Terminal Body -->
      <div id="terminal" class="p-4 overflow-y-auto" ref="terminalBody">
        <div v-if="!isTyping">
          <!-- Welcome Message -->
          <div class="line">
            <span class="text-green-400">$</span> Welcome to <strong class="text-white">Mohammad Javad Raei</strong>'s terminal portfolio.
          </div>
          <div class="line">
            <span class="text-green-400">$</span> Type <code class="bg-gray-700 text-yellow-300 rounded px-1">help</code> to see available commands.
          </div>

          <!-- History of Commands -->
          <div v-for="(output, index) in history" :key="index" class="line">
            <div v-if="output.command" class="flex items-center">
              <span class="text-green-400">$</span>
              <span class="ml-2 text-white">{{ output.command }}</span>
            </div>
            <pre class="whitespace-pre-wrap mt-1 text-gray-400 text-sm font-mono">{{ output.response }}</pre>
          </div>
        </div>

        <!-- Current Input Line -->
        <div class="flex items-center mt-2">
          <span class="text-green-400">$</span>
          <span class="ml-2 text-white">{{ currentCommand }}</span>
          <span class="cursor-blink bg-white w-2 h-4 ml-1"></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick } from 'vue';

const history = ref([]);
const currentCommand = ref('');
const terminalBody = ref(null);
const isTyping = ref(false);

const sections = {
  help: `Available commands:\n- about\n- skills\n- projects\n- contact\n- clear`,
  about: `I'm Mohammad Javad Raei, a passionate developer with experience in frontend and backend technologies.
I enjoy building modern web applications with clean UI and smooth UX.`,
  skills: `Skills:\n- JavaScript, HTML, CSS\n- Vue.js, Django\n- Python, Git, Linux\n- TailwindCSS, REST APIs`,
  projects: `Projects:\n- Terminal Portfolio (this site)\n- RESTful Blog API using Django\nCheck my GitHub for more!
🌐 https://github.com/mjraei`,
  contact: `You can contact me at:
📧 mjraei@example.com
🌐 https://github.com/mjraei`,
};

const handleCommand = (cmd) => {
  let response = '';
  if (cmd === 'clear') {
    history.value = [];
    return;
  } else if (sections[cmd]) {
    response = sections[cmd];
  } else {
    response = `Unknown command: ${cmd}`;
  }

  history.value.push({ command: cmd, response: '' });
  isTyping.value = true;
  typeResponse(response, () => {
    isTyping.value = false;
    currentCommand.value = '';
    scrollToBottom();
  });
};

const typeResponse = (text, callback) => {
  let i = 0;
  const lastIndex = history.value.length - 1;
  const typeChar = () => {
    if (i < text.length) {
      history.value[lastIndex].response += text.charAt(i);
      i++;
      setTimeout(typeChar, 15);
    } else {
      callback();
    }
  };
  typeChar();
};

const scrollToBottom = () => {
  nextTick(() => {
    if (terminalBody.value) {
      terminalBody.value.scrollTop = terminalBody.value.scrollHeight;
    }
  });
};

const handleKeyDown = (e) => {
  if (isTyping.value) return;

  if (e.key === 'Backspace') {
    currentCommand.value = currentCommand.value.slice(0, -1);
  } else if (e.key === 'Enter') {
    handleCommand(currentCommand.value.trim().toLowerCase());
  } else if (e.key.length === 1 && !e.ctrlKey && !e.metaKey) {
    currentCommand.value += e.key;
  }
};

onMounted(() => {
  window.addEventListener('keydown', handleKeyDown);
  scrollToBottom();
});

onUnmounted(() => {
  window.removeEventListener('keydown', handleKeyDown);
});
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;700&display=swap');

.font-mono {
  font-family: 'Fira Code', 'Courier New', monospace;
}

.window-container {
  min-height: 500px;
  @apply md:min-h-[600px];
}

#terminal {
  min-height: 400px;
  max-height: 70vh;
}

.cursor-blink {
  animation: blink 1s step-end infinite;
}

@keyframes blink {
  50% {
    opacity: 0;
  }
}
</style>
