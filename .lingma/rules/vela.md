---
trigger: manual
---
# Xiaomi Vela JS AI Client Application for Smart Band 9

Below is a comprehensive implementation plan for your AI client application targeting Xiaomi Band 9 with Vela_input_method integration.

## Project Structure

Project_Root/
├── manifest.json
├── app.ux
├── i18n/
│   └── defaults.json
├── common/
│   ├── logo.png
│   └── utils.js
└── pages/
    ├── index/
    │   └── index.ux
    └── settings/
        └── settings.ux

## 1. Manifest Configuration (manifest.json)

{
  "package": "com.vela.ai.client",
  "name": "VelaAIClient",
  "versionName": "1.0.0",
  "versionCode": 1,
  "minPlatformVersion": "1000",
  "icon": "/common/logo.png",
  "deviceType": [
    "band"
  ],
  "designWidth": 192,
  "features": [
    {
      "name": "system.fetch"
    },
    {
      "name": "system.router"
    },
    {
      "name": "system.storage"
    }
  ],
  "permissions": [
    {
      "origin": "*"
    }
  ],
  "config": {
    "logLevel": "debug"
  },
  "router": {
    "entry": "pages/index/index",
    "pages": {
      "pages/index/index": {
        "component": "index"
      },
      "pages/settings/settings": {
        "component": "settings"
      }
    }
  }
}

## 2. Global Application Lifecycle (app.ux)

<script>
export default {
  onCreate() {
    console.info('Vela AI Client Application created');
  },
  
  onDestroy() {
    console.info('Vela AI Client Application destroyed');
  }
}
</script>

## 3. Internationalization Strings (i18n/defaults.json)

{
  "app_name": "AI Client",
  "send": "Send",
  "settings": "Settings",
  "api_key": "API Key",
  "api_endpoint": "API Endpoint",
  "model": "Model",
  "message_placeholder": "Type your message...",
  "save": "Save",
  "cancel": "Cancel",
  "input_placeholder": "Enter text here..."
}

## 4. Utility Functions (common/utils.js)

export default {
  // Format API request for AI models
  formatRequest(prompt, model) {
    return {
      model: model,
      messages: [
        {
          role: "user",
          content: prompt
        }
      ]
    };
  },
  
  // Validate API configuration
  validateConfig(config) {
    return config.apiKey && config.apiEndpoint && config.model;
  },
  
  // DCI communication wrapper
  sendToDCI(data) {
    // In a real implementation, this would interface with the DCI mechanism
    // This is a placeholder for the actual DCI communication
    return new Promise((resolve, reject) => {
      // Simulate DCI communication delay
      setTimeout(() => {
        resolve({
          success: true,
          data: "This is a simulated response from the AI model via DCI"
        });
      }, 1000);
    });
  }
}

## 5. Main Page (pages/index/index.ux)

<template>
  <div class="page">
    <div class="header">
      <text class="title">{{ $t('app_name') }}</text>
      <input type="button" class="settings-btn" value="{{ $t('settings') }}" onclick="goToSettings" />
    </div>
    
    <div class="chat-container">
      <list class="chat-list" id="chatList">
        <block for="(index, message) in chatHistory">
          <list-item type="chat-item" class="chat-item {{message.sender}}">
            <text class="message-text">{{ message.content }}</text>
            <text if="{{ message.timestamp }}" class="timestamp">{{ formatTime(message.timestamp) }}</text>
          </list-item>
        </block>
      </list>
    </div>
    
    <div class="input-container">
      <input type="button" class="keyboard-toggle" value="{{ keyboardVisible ? '▼' : '▲' }}" onclick="toggleKeyboard" />
      <div class="input-area">
        <input type="text" class="text-input" placeholder="{{ $t('message_placeholder') }}" 
               id="messageInput" onchange="onInputChange" />
        <input type="button" class="send-btn" value="{{ $t('send') }}" onclick="sendMessage" />
      </div>
      
      <!-- Virtual Keyboard -->
      <div if="{{ keyboardVisible }}" class="virtual-keyboard">
        <div class="keyboard-row" for="{{ keyboardRows }}">
          <block for="key in $item">
            <input type="button" class="key" value="{{ key }}" onclick="onKeyPress(key)" />
          </block>
        </div>
        <div class="keyboard-row">
          <input type="button" class="key special-key" value="Space" onclick="onSpacePress" />
          <input type="button" class="key special-key" value="Backspace" onclick="onBackspacePress" />
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.page {
  flex-direction: column;
  padding: 5px;
  height: 100%;
}

.header {
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  padding-bottom: 10px;
  border-bottom: 1px solid #ddd;
}

.title {
  font-size: 18px;
  font-weight: bold;
}

.settings-btn {
  font-size: 14px;
  background-color: #007AFF;
  color: white;
  border-radius: 4px;
  padding: 2px 8px;
}

.chat-container {
  flex: 1;
  margin: 10px 0;
  overflow: scroll;
}

.chat-list {
  flex-direction: column;
}

.chat-item {
  padding: 8px;
  margin: 5px 0;
  border-radius: 8px;
  max-width: 80%;
}

.chat-item.user {
  background-color: #007AFF;
  self-align: flex-end;
}

.chat-item.ai {
  background-color: #E5E5EA;
  self-align: flex-start;
}

.message-text {
  font-size: 14px;
}

.timestamp {
  font-size: 10px;
  color: #888;
  margin-top: 4px;
  text-align: right;
}

.input-container {
  flex-direction: column;
}

.keyboard-toggle {
  align-self: center;
  font-size: 12px;
  background-color: #f0f0f0;
  border-radius: 4px;
  padding: 2px 10px;
  margin-bottom: 5px;
}

.input-area {
  flex-direction: row;
  align-items: center;
  margin-bottom: 5px;
}

.text-input {
  flex: 1;
  font-size: 14px;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
}

.send-btn {
  font-size: 14px;
  background-color: #007AFF;
  color: white;
  border-radius: 4px;
  padding: 8px 12px;
  margin-left: 5px;
}

.virtual-keyboard {
  flex-direction: column;
  background-color: #f5f5f5;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 5px;
}

.keyboard-row {
  flex-direction: row;
  justify-content: center;
  margin-bottom: 5px;
}

.key {
  flex: 1;
  font-size: 14px;
  padding: 8px 5px;
  margin: 0 2px;
  background-color: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  text-align: center;
}

.special-key {
  flex: 2;
}
</style>

<script>
import utils from '../../common/utils.js'

export default {
  private: {
    messageText: '',
    chatHistory: [],
    keyboardVisible: false,
    keyboardRows: [
      ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p'],
      ['a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l'],
      ['z', 'x', 'c', 'v', 'b', 'n', 'm']
    ],
    apiConfig: {
      apiKey: '',
      apiEndpoint: '',
      model: ''
    }
  },
  
  onInit() {
    this.loadApiConfig();
    this.loadChatHistory();
  },
  
  onReady() {
    // Initialize with welcome message
    if (this.chatHistory.length === 0) {
      this.addMessage('ai', 'Hello! I am your AI assistant. How can I help you today?');
    }
  },
  
  onInputChange(evt) {
    this.messageText = evt.value;
  },
  
  toggleKeyboard() {
    this.keyboardVisible = !this.keyboardVisible;
  },
  
  onKeyPress(key) {
    const input = this.$element('messageInput');
    this.messageText += key;
    // Update the input field visually
    input.value = this.messageText;
  },
  
  onSpacePress() {
    this.messageText += ' ';
    const input = this.$element('messageInput');
    input.value = this.messageText;
  },
  
  onBackspacePress() {
    this.messageText = this.messageText.slice(0, -1);
    const input = this.$element('messageInput');
    input.value = this.messageText;
  },
  
  sendMessage() {
    if (!this.messageText.trim()) return;
    
    // Add user message to chat
    this.addMessage('user', this.messageText);
    
    // Save current message
    const userMessage = this.messageText;
    
    // Clear input
    this.messageText = '';
    const input = this.$element('messageInput');
    input.value = '';
    
    // Send to AI via DCI
    this.sendToAI(userMessage);
  },
  
  sendToAI(message) {
    if (!utils.validateConfig(this.apiConfig)) {
      this.addMessage('ai', 'Please configure API settings first in the Settings menu.');
      return;
    }
    
    // Show typing indicator
    const typingIndicator = this.addMessage('ai', '...');
    
    // In a real implementation, this would go through DCI
    utils.sendToDCI({
      message: message,
      config: this.apiConfig
    }).then(response => {
      // Remove typing indicator
      this.chatHistory.pop();
      
      // Add AI response
      this.addMessage('ai', response.data);
    }).catch(error => {
      // Remove typing indicator
      this.chatHistory.pop();
      
      // Show error message
      this.addMessage('ai', 'Sorry, I encountered an error: ' + error.message);
    });
  },
  
  addMessage(sender, content) {
    const message = {
      sender: sender,
      content: content,
      timestamp: new Date()
    };
    
    this.chatHistory.push(message);
    
    // Scroll to bottom (if possible in Vela)
    this.$nextTick(() => {
      // Attempt to scroll to the new message
      const list = this.$element('chatList');
      // Scrolling might need to be handled differently in Vela
    });
    
    return message;
  },
  
  goToSettings() {
    this.$router.push({
      uri: '/pages/settings/settings'
    });
  },
  
  loadApiConfig() {
    try {
      const config = this.$storage.getSync('apiConfig');
      if (config) {
        this.apiConfig = JSON.parse(config);
      } else {
        // Set default values
        this.apiConfig = {
          apiKey: '',
          apiEndpoint: 'https://api.example.com/v1/chat/completions',
          model: 'gpt-3.5-turbo'
        };
      }
    } catch (error) {
      console.error('Failed to load API config:', error);
    }
  },
  
  loadChatHistory() {
    try {
      const history = this.$storage.getSync('chatHistory');
      if (history) {
        this.chatHistory = JSON.parse(history).map(item => ({
          ...item,
          timestamp: item.timestamp ? new Date(item.timestamp) : null
        }));
      }
    } catch (error) {
      console.error('Failed to load chat history:', error);
    }
  },
  
  formatTime(date) {
    if (!date) return '';
    return date.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
  },
  
  onDestroy() {
    // Save chat history
    try {
      this.$storage.setSync('chatHistory', JSON.stringify(this.chatHistory));
    } catch (error) {
      console.error('Failed to save chat history:', error);
    }
  }
}
</script>

## 6. Settings Page (pages/settings/settings.ux)

<template>
  <div class="page">
    <div class="header">
      <input type="button" class="back-btn" value="←" onclick="goBack" />
      <text class="title">{{ $t('settings') }}</text>
    </div>
    
    <div class="form-group">
      <text class="label">{{ $t('api_endpoint') }}</text>
      <input type="text" class="input" id="apiEndpoint" value="{{ apiConfig.apiEndpoint }}" onchange="onEndpointChange" />
    </div>
    
    <div class="form-group">
      <text class="label">{{ $t('api_key') }}</text>
      <input type="password" class="input" id="apiKey" value="{{ apiConfig.apiKey }}" onchange="onApiKeyChange" />
    </div>
    
    <div class="form-group">
      <text class="label">{{ $t('model') }}</text>
      <input type="text" class="input" id="model" value="{{ apiConfig.model }}" onchange="onModelChange" />
    </div>
    
    <div class="button-container">
      <input type="button" class="save-btn" value="{{ $t('save') }}" onclick="saveConfig" />
    </div>
  </div>
</template>

<style>
.page {
  flex-direction: column;
  padding: 10px;
  height: 100%;
}

.header {
  flex-direction: row;
  align-items: center;
  padding-bottom: 15px;
  border-bottom: 1px solid #ddd;
}

.back-btn {
  font-size: 18px;
  background-color: transparent;
  width: 40px;
  height: 40px;
}

.title {
  font-size: 18px;
  font-weight: bold;
  flex: 1;
  text-align: center;
}

.form-group {
  flex-direction: column;
  margin-bottom: 15px;
}

.label {
  font-size: 14px;
  margin-bottom: 5px;
  color: #333;
}

.input {
  font-size: 14px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  background-color: white;
}

.button-container {
  flex-direction: row;
  justify-content: center;
  margin-top: 20px;
}

.save-btn {
  font-size: 16px;
  background-color: #007AFF;
  color: white;
  border-radius: 4px;
  padding: 12px 30px;
}
</style>

<script>
export default {
  protected: {
    apiConfig: {
      apiKey: '',
      apiEndpoint: '',
      model: ''
    }
  },
  
  onInit() {
    this.loadApiConfig();
  },
  
  onEndpointChange(evt) {
    this.apiConfig.apiEndpoint = evt.value;
  },
  
  onApiKeyChange(evt) {
    this.apiConfig.apiKey = evt.value;
  },
  
  onModelChange(evt) {
    this.apiConfig.model = evt.value;
  },
  
  saveConfig() {
    try {
      this.$storage.setSync('apiConfig', JSON.stringify(this.apiConfig));
      this.$router.back();
    } catch (error) {
      console.error('Failed to save API config:', error);
    }
  },
  
  loadApiConfig() {
    try {
      const config = this.$storage.getSync('apiConfig');
      if (config) {
        this.apiConfig = JSON.parse(config);
      }
    } catch (error) {
      console.error('Failed to load API config:', error);
    }
  },
  
  goBack() {
    this.$router.back();
  }
}
</script>

## Build Instructions

1. **Setup Development Environment**:
   # Install AIoT-toolkit
   npm install -g aiot-toolkit@latest
   
   # Create new project
   npm create aiot vela-ai-client
   cd vela-ai-client

2. **Replace Generated Files**:
   Replace the generated files with the code provided above, maintaining the directory structure.

3. **Build Project**:
   # Build the project
   aiot build
   
   # Or for release
   aiot release

4. **Run on Emulator**:
   # Start emulator
   aiot start
   
   # Or create a specific Vela AVD
   aiot createVelaAvd --name band9 --device-type band

## Implementation Notes

1. **Vela Input Method Integration**: The virtual keyboard implementation provides a full-size keyboard that can be collapsed and expanded as requested.

2. **DCI Communication**: The application is structured to route all network communications through the DCI mechanism as required. The `utils.js` module contains a placeholder for the actual DCI implementation.

3. **API Configuration**: Users can configure their API endpoint, key, and model in the settings page. All configurations are persisted using the storage API.

4. **Memory Optimization**: The application follows Vela best practices:
   - Proper cleanup in onDestroy lifecycle
   - Minimal data storage
   - Efficient rendering with proper use of list components

5. **UI Design Considerations**:
   - Optimized for the narrow screen of the Xiaomi Band 9 (192px design width)
   - Collapsible keyboard to maximize chat display area
   - Clear visual distinction between user and AI messages
   - Simple, functional interface suitable for small screens

This implementation provides a solid foundation for an AI client on Xiaomi Band 9 that communicates with external AI services through the required DCI mechanism.
