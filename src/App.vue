<script>
import profilePhoto from "/src/assets/Batuhan_Photo.png";
import bg1 from "/src/assets/bg-1.png";
import bg2 from "/src/assets/bg-2.png";

export default {
  name: 'HeroSection',
  data() {
    return {
      profilePhoto,
      bg1,
      bg2,
      isScrolled: false,
      activeSection: 'home',
      
      // --- CONTACT FORM DATA ---
      form: {
        name: '',
        email: '',
        message: ''
      },

      // --- CHATBOT DATA ---
      isChatOpen: false,       // Is the chat window open?
      chatInput: '',           // Current text in chat box
      isChatLoading: false,    // Is AI thinking?
      messages: [              // Chat history
        { text: "Hi! I am Kenneth's AI assistant. Ask me anything about his projects or skills.", isUser: false }
      ]
    };
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll);
  },
  methods: {
    handleScroll() {
      this.isScrolled = window.scrollY > 50;
    },
    scrollToSection(sectionId) {
      const element = document.getElementById(sectionId);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
        this.activeSection = sectionId; 
      }
    },

    // --- CONTACT FORM SUBMISSION (Standard) ---
    submitContactForm() {
      if(this.form.name && this.form.email) {
        alert(`Thanks, ${this.form.name}! I received your message.`);
        this.form.name = '';
        this.form.email = '';
        this.form.message = '';
      } else {
        alert("Please fill in your name and email.");
      }
    },

    // --- CHATBOT METHODS ---
    toggleChat() {
      this.isChatOpen = !this.isChatOpen;
    },

    async query(data) {
        const response = await fetch(
            "http://localhost:3000/api/v1/prediction/a567f57f-bb61-42d9-b1d0-4455d4637a19",
            {
                method: "POST",
                headers: { "Content-Type": "application/json" },
                body: JSON.stringify(data)
            }
        );
        const result = await response.json();
        return result;
    },

    async sendChatMessage() {
      if (!this.chatInput.trim()) return;

      // 1. Add User Message
      const userMsg = this.chatInput;
      this.messages.push({ text: userMsg, isUser: true });
      this.chatInput = '';
      this.isChatLoading = true;

      // Scroll to bottom
      this.$nextTick(() => {
        const chatBody = this.$refs.chatBody;
        if(chatBody) chatBody.scrollTop = chatBody.scrollHeight;
      });

      try {
        // 2. Call API
        const result = await this.query({ "question": userMsg });
        
        // 3. Add AI Response (Adjust 'result.text' based on your specific API return)
        const aiText = result.text || result.answer || JSON.stringify(result);
        
        this.messages.push({ text: aiText, isUser: false });

      } catch (error) {
        console.error(error);
        this.messages.push({ text: "Sorry, I can't connect to the server right now.", isUser: false });
      } finally {
        this.isChatLoading = false;
        // Scroll to bottom again
        this.$nextTick(() => {
          const chatBody = this.$refs.chatBody;
          if(chatBody) chatBody.scrollTop = chatBody.scrollHeight;
        });
      }
    }
  }
};
</script>

<template>
  <div id="home" class="container" :style="{ backgroundImage: `url(${bg2}), url(${bg1})` }">
    <nav class="navbar" :class="{ 'scrolled': isScrolled }">
      <ul>
        <li :class="{ active: activeSection === 'home' }" @click="scrollToSection('home')">Home</li>
        <li :class="{ active: activeSection === 'about' }" @click="scrollToSection('about')">About</li>
        <li :class="{ active: activeSection === 'projects' }" @click="scrollToSection('projects')">Project</li>
        <li :class="{ active: activeSection === 'contact' }" @click="scrollToSection('contact')">Contact</li>
      </ul>
    </nav>
    <div class="content">
       <div class="text">
        <p class="hello">Hello, I am</p>
        <h1 class="name name-gradient">KENNETH</h1>
        <h1 class="name name-gradient">JAMES</h1>
        <p class="role">and I am a <span>Front-end Developer</span></p>
      </div>
      <div class="image-wrapper">
        <img :src="profilePhoto" class="profile-img" />
      </div>
    </div>
  </div>

  <div id="about" class="container-2">
    <div class="content-2">
      <div class="text-center">
        <p class="section-title"><b>ABOUT ME</b></p>
        <div class="about-card">
          <p>
            I am a <b>Frontend Developer</b> with a strong background in UI/UX design.
            I combine technical expertise in <b>Vue.js, C#, and PHP</b> with creative tools like 
            <b>Blender</b> to build responsive and visually rich applications.
          </p>
          <br>
          <p>
            My passion lies at the intersection of development and design, ensuring that every 
            line of code contributes to a seamless user experience.
          </p>
        </div>
      </div>
    </div>
  </div>

  <div id="projects" class="container-3">
      <div class="content-3">
      <p class="section-title" style="color: black;"><b>PROJECTS</b></p>
      
      <div class="projects-grid">
        <div class="project-card">
          <h3>LuTuon</h3>
          <p class="project-subtitle">Gamified Mobile Cooking Simulator</p>
          <p class="project-desc">
            A mobile simulator for Filipino cuisine built with C# and Unity. 
            Featuring procedural materials created in Blender. 
            <b>Top 25 Qualifier, PSC X Region 7 (2025).</b>
          </p>
          <div class="tags">
            <span>C#</span>
            <span>Unity</span>
            <span>Blender</span>
          </div>
        </div>

        <div class="project-card">
          <h3>Borrow Hon</h3>
          <p class="project-subtitle">Library Management System</p>
          <p class="project-desc">
            A user-centric platform designed to streamline book borrowing workflows. 
            Focused on accessibility and improving user satisfaction.
          </p>
          <div class="tags">
            <span>UI/UX</span>
            <span>Database</span>
            <span>Design</span>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div id="contact" class="container-4">
    <div class="content-4">
      <p class="section-title"><b>CONTACT</b></p>
      
      <div class="contact-card">
        <p class="contact-header">Interested in working together? Let's connect.</p>
        
        <form @submit.prevent="submitContactForm">
          <div class="form-group">
            <label>Name</label>
            <input type="text" v-model="form.name" placeholder="Enter your name" />
          </div>
          
          <div class="form-group">
            <label>Email</label>
            <input type="email" v-model="form.email" placeholder="Enter your email address" />
          </div>
          
          <div class="form-group">
            <label>Message</label>
            <textarea v-model="form.message" rows="4" placeholder="Write your message here..."></textarea>
          </div>
          
          <button type="submit" class="send-btn">SEND MESSAGE</button>
        </form>

        <div class="contact-links">
           <a href="mailto:kennethjamesbatuhan@gmail.com">kennethjamesbatuhan@gmail.com</a>
           <span> | </span>
           <a href="https://github.com/Kenronix" target="_blank">github.com/Kenronix</a>
        </div>
      </div>
    </div>
  </div>

  <footer class="footer">
    <p>&copy; 2025 Kenneth James Batuhan. All rights reserved.</p>
  </footer>

  <div class="chatbot-widget">
    <transition name="slide-fade">
      <div v-if="isChatOpen" class="chat-window">
        <div class="chat-header">
          <span>AI Assistant</span>
          <button @click="toggleChat" class="close-btn">×</button>
        </div>
        
        <div class="chat-body" ref="chatBody">
          <div v-for="(msg, index) in messages" :key="index" 
               class="message" :class="{ 'user-msg': msg.isUser, 'ai-msg': !msg.isUser }">
            {{ msg.text }}
          </div>
          <div v-if="isChatLoading" class="message ai-msg loading-dots">...</div>
        </div>

        <div class="chat-footer">
          <input type="text" v-model="chatInput" @keyup.enter="sendChatMessage" placeholder="Type a message..." />
          <button @click="sendChatMessage">➤</button>
        </div>
      </div>
    </transition>

    <button class="chat-button" @click="toggleChat">
      <span v-if="!isChatOpen">💬</span>
      <span v-else>▼</span>
    </button>
  </div>

</template>

<style scoped>
/* --- BASE LAYOUT --- */
.container {
  width: 100%;
  min-height: 100vh;
  overflow-x: hidden; 
  background-position: bottom center, top center;
  background-size: cover, cover;
  background-repeat: no-repeat;
  display: flex;
  flex-direction: column;
}

/* --- FIXED NAVBAR --- */
.navbar {
  position: fixed; 
  top: 0;
  left: 0;
  width: 100%;
  z-index: 1000;
  background-color: transparent;
  padding-top: 50px;
  padding-bottom: 20px;
  border-bottom: 1px solid transparent;
  display: flex;
  justify-content: center;
  transition: all 0.3s ease-in-out;
}

.navbar.scrolled {
  padding-top: 20px; 
  padding-bottom: 20px;
  background-color: rgba(36, 36, 36, 0.95); 
  backdrop-filter: blur(15px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.1); 
  box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
}

.navbar ul {
  list-style: none;
  display: flex;
  gap: 20px; 
  margin: 0;
  padding: 0;
}

.navbar li {
  color: white;
  cursor: pointer;
  font-family: 'Montserrat', sans-serif;
  font-size: 16px; 
  transition: color 0.3s ease;
}

.navbar li:hover {
  color: #00ff40;
}

.navbar .active {
  color: #00ff40;
  border-bottom: 2px solid #00ff40;
}

/* CONTENT CONTAINER */
.content {
  flex: 1; 
  display: flex;
  flex-direction: column; 
  align-items: center;    
  justify-content: center; 
  text-align: center;      
  padding: 20px;
  margin-top: 100px; 
}

/* TEXT STYLES */
.text .hello {
  font-size: 18px;
  color: white;
  margin-bottom: 10px;
  font-family: 'Montserrat', sans-serif;
}

.name {
  margin: 0;
  font-size: clamp(60px, 12vw, 200px); 
  line-height: 0.9;
  color: #00ff40;
  letter-spacing: 5px;
  font-family: 'Bebas Neue', sans-serif;
}

.name-gradient {
  background: linear-gradient(50deg, #00ff40 0%, #003f00 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.role {
  font-size: 18px;
  margin-top: 20px;
  color: white;
  font-family: 'Montserrat', sans-serif;
}

.role span {
  color: #00ff40;
  font-weight: bold;
}

/* IMAGE STYLES */
.image-wrapper {
  margin-top: 40px;
  width: 100%;
  display: flex;
  justify-content: center;
}

.profile-img {
  width: 100%;
  max-width: 400px; 
  height: auto;
  border-radius: 10px;
}

/* --- SECTIONS COMMON STYLES --- */
.section-title {
  font-size: 32px;
  font-family: 'Bebas Neue', sans-serif;
  letter-spacing: 2px;
  margin-bottom: 40px;
  color: white;
}

/* --- ABOUT SECTION --- */
.container-2 {
  min-height: 80vh; 
  background-color: #1a1a1a;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 60px 20px;
}

.content-2 {
  max-width: 800px;
  text-align: center;
}

.about-card p {
  font-size: 20px;
  color: #ccc;
  line-height: 1.6;
  font-family: 'Montserrat', sans-serif;
}

.about-card b {
  color: #00ff40;
}

/* --- PROJECTS SECTION --- */
.container-3 {
  min-height: 80vh; 
  background-color: #f5f5f5; 
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 80px 20px;
}

.projects-grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 30px;
  width: 100%;
  max-width: 1000px;
}

.project-card {
  background: white;
  padding: 30px;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  text-align: left;
}

.project-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 40px rgba(0,0,0,0.15);
}

.project-card h3 {
  font-family: 'Bebas Neue', sans-serif;
  font-size: 28px;
  margin: 0 0 5px 0;
  color: #333;
}

.project-subtitle {
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  color: #00b32d;
  font-weight: bold;
  margin-bottom: 15px;
}

.project-desc {
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  color: #555;
  margin-bottom: 20px;
  line-height: 1.5;
}

.tags {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.tags span {
  background-color: #e0ffe6;
  color: #00661a;
  padding: 5px 12px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: bold;
  font-family: 'Montserrat', sans-serif;
}

/* --- UPDATED: CONTACT SECTION STYLES --- */
.container-4 {
  min-height: 90vh;
  background-color: #111;
  display: flex;
  flex-direction: column;
  justify-content: center; /* Vertically center */
  align-items: center;     /* Horizontally center */
  padding: 60px 20px;
  text-align: center;
}

/* 1. Added explicit width and centering to content-4 */
.content-4 {
  width: 100%; 
  display: flex;
  flex-direction: column;
  align-items: center;
}

.contact-card {
  background-color: #1a1a1a;
  padding: 40px;
  border-radius: 15px;
  width: 100%;
  max-width: 600px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  border: 1px solid #333;
  margin: 0 auto; /* 2. Ensures card is centered if flex fails */
}

.contact-header {
  color: #888;
  font-family: 'Montserrat', sans-serif;
  font-size: 18px;
  margin-bottom: 30px;
}

.form-group {
  margin-bottom: 20px;
  text-align: left;
}

.form-group label {
  display: block;
  color: #00ff40;
  font-family: 'Montserrat', sans-serif;
  font-size: 14px;
  margin-bottom: 8px;
  font-weight: bold;
}

/* 3. Changed width to 100% and added box-sizing */
input, textarea {
  width: 100%; 
  box-sizing: border-box; /* Prevents padding from breaking width */
  padding: 12px;
  background-color: #2a2a2a;
  border: 1px solid #444;
  border-radius: 8px;
  color: white;
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  transition: all 0.3s;
}

input:focus, textarea:focus {
  outline: none;
  border-color: #00ff40;
  background-color: #333;
}

.send-btn {
  width: 100%;
  padding: 15px;
  background-color: #00ff40;
  color: #003f00;
  font-family: 'Bebas Neue', sans-serif;
  font-size: 24px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  letter-spacing: 2px;
  transition: transform 0.2s, background-color 0.2s;
  margin-top: 10px;
}

.send-btn:hover {
  background-color: #00cc33;
  transform: scale(1.02);
}

.contact-links {
  margin-top: 30px;
  font-family: 'Montserrat', sans-serif;
  font-size: 14px;
  color: #666;
}

.contact-links a {
  color: #888;
  text-decoration: none;
  transition: color 0.3s;
}

.contact-links a:hover {
  color: #00ff40;
}

/* --- FOOTER --- */
.footer {
  background-color: black;
  color: #555;
  text-align: center;
  padding: 20px;
  font-family: 'Montserrat', sans-serif;
  font-size: 14px;
}

/* --- DESKTOP VIEW --- */
@media (min-width: 1024px) {
  .navbar {
    padding-top: 60px;
    padding-bottom: 30px;
  }
  
  .navbar ul {
    gap: 40px;
  }

  .navbar li {
    font-size: 20px;
  }

  .content {
    flex-direction: row; 
    justify-content: space-between;
    text-align: left; 
    padding: 0 10%; 
  }

  .text .hello {
    font-size: 24px;
    margin-bottom: 20px;
  }

  .name {
    letter-spacing: 10px;
  }

  .role {
    font-size: 28px;
  }

  .image-wrapper {
    margin-top: 0;
    width: auto;
    display: block;
  }

  .profile-img {
    max-width: 700px; 
  }

  .projects-grid {
    grid-template-columns: 1fr 1fr;
  }
}

.chatbot-widget {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 9999;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

/* Floating Toggle Button */
.chat-button {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: #00ff40;
  border: none;
  color: #003f00;
  font-size: 30px;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(0, 255, 64, 0.4);
  transition: transform 0.3s, background-color 0.3s;
  display: flex;
  justify-content: center;
  align-items: center;
}

.chat-button:hover {
  transform: scale(1.1);
  background-color: #00cc33;
}

/* Chat Window */
.chat-window {
  width: 350px;
  height: 450px;
  background-color: #1e1e1e;
  border: 1px solid #333;
  border-radius: 12px;
  box-shadow: 0 10px 40px rgba(0,0,0,0.5);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  margin-bottom: 15px; /* Space between window and button */
  font-family: 'Montserrat', sans-serif;
}

/* Header */
.chat-header {
  background-color: #00ff40;
  color: #003f00;
  padding: 15px;
  font-weight: bold;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #003f00;
  line-height: 1;
}

/* Body (Messages) */
.chat-body {
  flex: 1;
  padding: 15px;
  overflow-y: auto;
  background-color: #1a1a1a;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

/* Messages */
.message {
  max-width: 80%;
  padding: 10px 14px;
  border-radius: 10px;
  font-size: 14px;
  line-height: 1.4;
  word-wrap: break-word;
}

.user-msg {
  align-self: flex-end;
  background-color: #00ff40;
  color: #003f00;
  border-bottom-right-radius: 2px;
}

.ai-msg {
  align-self: flex-start;
  background-color: #333;
  color: #ddd;
  border-bottom-left-radius: 2px;
}

.loading-dots {
  font-style: italic;
  color: #888;
}

/* Footer (Input) */
.chat-footer {
  padding: 10px;
  background-color: #222;
  border-top: 1px solid #333;
  display: flex;
  gap: 10px;
}

.chat-footer input {
  flex: 1;
  padding: 10px;
  border-radius: 20px;
  border: 1px solid #444;
  background-color: #333;
  color: white;
  outline: none;
}

.chat-footer input:focus {
  border-color: #00ff40;
}

.chat-footer button {
  background: none;
  border: none;
  color: #00ff40;
  font-size: 20px;
  cursor: pointer;
}

.chat-footer button:hover {
  color: white;
}

/* Animations */
.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}
.slide-fade-enter-from, .slide-fade-leave-to {
  opacity: 0;
  transform: translateY(20px);
}
</style>