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
      isScrolled: false,     // Tracks if user has scrolled down
      activeSection: 'home', // Tracks which section is currently visible
      
      // --- CONTACT FORM DATA ---
      form: {
        name: '',
        email: '',
        message: ''
      },

      // --- CHATBOT DATA ---
      isChatOpen: false,       
      chatInput: '',           
      isChatLoading: false,    
      messages: [] // Start empty so the Welcome Screen shows first
    };
  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);
    this.handleScroll(); 
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll);
  },
  methods: {
    handleScroll() {
      this.isScrolled = window.scrollY > 50;

      const sections = ['home', 'about', 'projects', 'contact'];
      const scrollPosition = window.scrollY + 250; 

      for (const section of sections) {
        const element = document.getElementById(section);
        if (element) {
          const top = element.offsetTop;
          const height = element.offsetHeight;
          if (scrollPosition >= top && scrollPosition < top + height) {
            this.activeSection = section;
          }
        }
      }
    },

    scrollToSection(sectionId) {
      const element = document.getElementById(sectionId);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
        this.activeSection = sectionId; 
      }
    },

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

    // UPDATED QUERY METHOD
    async query(data) {
        try {
            const response = await fetch(
                "https://cloud.flowiseai.com/api/v1/prediction/a5d16278-9b65-4294-90a3-eb9cddea18ce",
                {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json"
                    },
                    body: JSON.stringify(data)
                }
            );
            const result = await response.json();
            return result;
        } catch (error) {
            console.error("API Error:", error);
            throw error;
        }
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
        // 2. Call API (Updated to use the new cloud URL)
        const result = await this.query({ "question": userMsg });
        
        // 3. Add AI Response
        const aiText = result.text || result.answer || JSON.stringify(result);
        this.messages.push({ text: aiText, isUser: false });

      } catch (error) {
        console.error(error);
        this.messages.push({ text: "Sorry, I can't connect to the server right now.", isUser: false });
      } finally {
        this.isChatLoading = false;
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
        
        <div class="action-container">
          
          <a href="/Batuhan_CV.pdf" download="Batuhan_CV.pdf" class="download-cv-btn">
            DOWNLOAD CV
          </a>

          <div class="social-icons">
            <a href="https://github.com/Kenronix" target="_blank" title="GitHub">
              <svg viewBox="0 0 24 24" fill="currentColor">
                <path d="M12 0c-6.626 0-12 5.373-12 12 0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23.957-.266 1.983-.399 3.003-.404 1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576 4.765-1.589 8.199-6.086 8.199-11.386 0-6.627-5.373-12-12-12z"/>
              </svg>
            </a>

            <a href="mailto:kennethjamesbatuhan@gmail.com" title="Email">
              <svg viewBox="0 0 24 24" fill="currentColor">
                <path d="M20 4H4c-1.1 0-1.99.9-1.99 2L2 18c0 1.1.9 2 2 2h16c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 4l-8 5-8-5V6l8 5 8-5v2z"/>
              </svg>
            </a>
          </div>

        </div>
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
          <a href="https://lutuon.app/" target="_blank" class="project-link">
            <h3>
              LuTuon 
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6"></path><polyline points="15 3 21 3 21 9"></polyline><line x1="10" y1="14" x2="21" y2="3"></line></svg>
            </h3>
          </a>
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
          <h3>BorrowHon</h3>
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
    <p>&copy; 2025 <b>Kenneth James Batuhan</b>. All rights reserved.</p>
  </footer>

  <div class="chatbot-widget">
    <transition name="slide-fade">
      <div v-if="isChatOpen" class="chat-window">
        <div class="chat-header">
          <span>Kenji AI</span>
          <button @click="toggleChat" class="close-btn">×</button>
        </div>
        
        <div class="chat-body" ref="chatBody" :class="{ 'has-messages': messages.length > 0 }">
          
          <div v-if="messages.length === 0" class="welcome-screen">
            <svg class="welcome-logo" viewBox="0 0 24 24" fill="currentColor">
              <path d="M12 2L14.4 9.6L22 12L14.4 14.4L12 22L9.6 14.4L2 12L9.6 9.6L12 2Z"/>
            </svg>
            <h2>Hello👋</h2>
            <p>How can I help you today?</p>
          </div>

          <div v-else class="message-list">
            <div v-for="(msg, index) in messages" 
                 :key="index" 
                 class="message-row" 
                 :class="{ 'user-row': msg.isUser, 'ai-row': !msg.isUser }">
              
              <div v-if="!msg.isUser" class="ai-avatar-chat">
                <svg viewBox="0 0 24 24" fill="currentColor">
                  <path d="M12 2L14.4 9.6L22 12L14.4 14.4L12 22L9.6 14.4L2 12L9.6 9.6L12 2Z"/>
                </svg>
              </div>

              <div class="message-bubble">
                {{ msg.text }}
              </div>
            </div>
          </div>

          <div v-if="isChatLoading" class="message-row ai-row">
              <div class="ai-avatar-chat">
                <svg viewBox="0 0 24 24" fill="currentColor"><path d="M12 2L14.4 9.6L22 12L14.4 14.4L12 22L9.6 14.4L2 12L9.6 9.6L12 2Z"/></svg>
              </div>
              <div class="message-bubble loading-dots">...</div>
          </div>

        </div>

        <div class="chat-footer">
          <input type="text" v-model="chatInput" @keyup.enter="sendChatMessage" placeholder="Type a message..." />
          <button @click="sendChatMessage">➤</button>
        </div>
      </div>
    </transition>

    <button class="chat-button" :class="{ 'is-open': isChatOpen }" @click="toggleChat">
      <div v-if="!isChatOpen" class="btn-content">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 2L14.4 9.6L22 12L14.4 14.4L12 22L9.6 14.4L2 12L9.6 9.6L12 2Z"/>
        </svg>
        <span>Ask Kenji</span>
      </div>
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

/* --- BUTTON & ICONS CONTAINER --- */
.action-container {
  display: flex;
  align-items: center;     
  justify-content: center; 
  gap: 20px;               
  margin-top: 35px;
  width: 100%;             
}

/* --- DOWNLOAD BUTTON --- */
.download-cv-btn {
  display: flex; 
  align-items: center;
  justify-content: center;
  padding: 12px 35px;
  border: 2px solid #00ff40;
  background-color: transparent;
  color: #00ff40;
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
  font-size: 16px;
  text-decoration: none;
  border-radius: 50px;
  letter-spacing: 1px;
  cursor: pointer;
  transition: all 0.3s ease-in-out;
}

.download-cv-btn:hover {
  background-color: #00ff40;
  color: #003f00;
  transform: translateY(-3px);
  box-shadow: 0 0 20px rgba(0, 255, 64, 0.4);
}

/* --- SOCIAL ICONS --- */
.social-icons {
  display: flex;
  gap: 15px;
}

.social-icons svg {
  width: 32px;
  height: 32px;
  color: white;
  transition: all 0.3s ease;
}

.social-icons a:hover svg {
  color: #00ff40;
  transform: translateY(-3px);
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

/* --- CONTACT SECTION --- */
.container-4 {
  min-height: 90vh;
  background-color: #111;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 60px 20px;
  text-align: center;
}

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
  margin: 0 auto; 
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

input, textarea {
  width: 100%; 
  box-sizing: border-box; 
  padding: 12px;
  background-color: #2a2a2a;
  border: 1px solid #444;
  border-radius: 8px;
  color: white;
  font-family: 'Montserrat', sans-serif;
  font-size: 16px;
  transition: all 0.3s;
  resize: none;
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
  
  .action-container {
    justify-content: flex-start;
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

/* ================================ */
/* CHATBOT STYLES            */
/* ================================ */
.chatbot-widget {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 9999;
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.chat-button {
  min-width: 60px; 
  height: 60px;
  border-radius: 50px; /* Pill shape */
  background-color: #00ff40; 
  color: #003f00;
  border: none;
  font-family: 'Montserrat', sans-serif;
  font-weight: bold;
  font-size: 16px;
  cursor: pointer;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  transition: all 0.4s ease;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 0 24px;
  white-space: nowrap;
}

.chat-button:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(125, 0, 255, 0.5);
}

.btn-content {
  display: flex;
  align-items: center;
  gap: 8px;
}

/* Style for open state (Circle) */
.chat-button.is-open {
  width: 60px;
  padding: 0;
  border-radius: 50%;
  background-color: #333;
  color: white;
}

/* CHAT WINDOW */
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
  margin-bottom: 15px; 
  font-family: 'Montserrat', sans-serif;
}

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

/* CHAT BODY & WELCOME SCREEN */
.chat-body {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  background-color: #1a1a1a;
  display: flex;
  flex-direction: column;
  justify-content: center; /* Centers welcome screen vertically */
}

/* When messages exist, align to top */
.chat-body.has-messages {
   justify-content: flex-start;
}

/* FIX FOR OVERLAP: 
  Ensure this list takes full width and manages spacing correctly 
*/
.message-list {
  display: flex;
  flex-direction: column;
  gap: 15px; 
  width: 100%;
}

/* FIX FOR OVERLAP: 
  Each row is a flex container. 
  'flex-shrink: 0' ensures it doesn't squash when the list gets long.
*/
.message-row {
  display: flex;
  align-items: flex-end;
  gap: 8px;            
  width: 100%;        /* Use full width of container */
  flex-shrink: 0;     /* Prevent squashing */
}

/* --- AI SPECIFIC STYLES --- */
.ai-row {
  justify-content: flex-start; /* Align Left */
}

.ai-avatar-chat {
  width: 30px;
  height: 30px;
  min-width: 30px; /* Important: stops circle from squishing */
  background-color: #00ff40;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #003f00;
}

.ai-avatar-chat svg {
  width: 16px;
  height: 16px;
}

/* --- USER SPECIFIC STYLES --- */
.user-row {
  justify-content: flex-end; /* Align Right */
}

/* --- BUBBLE STYLES --- */
.message-bubble {
  padding: 10px 14px;
  border-radius: 15px;
  font-size: 14px;
  line-height: 1.4;
  position: relative;
  max-width: 80%; /* Limit bubble width */
  word-wrap: break-word;
}

/* AI Bubble Color */
.ai-row .message-bubble {
  background-color: #333;
  color: #ddd;
  border-bottom-left-radius: 2px; 
}

/* User Bubble Color */
.user-row .message-bubble {
  background-color: #00ff40;
  color: #003f00;
  border-bottom-right-radius: 2px;
}

/* Welcome Screen Styles */
.welcome-screen {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  color: white;
}

.welcome-logo {
  width: 60px;
  height: 60px;
  color: #00ff40;
  margin-bottom: 20px;
}

.welcome-screen h2 {
  font-size: 22px;
  font-weight: bold;
  margin: 0 0 10px 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

.welcome-screen p {
  color: #aaa;
  font-size: 16px;
  margin: 0;
}

.loading-dots {
  font-style: italic;
  color: #888;
}

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

.slide-fade-enter-active, .slide-fade-leave-active {
  transition: all 0.3s ease;
}
.slide-fade-enter-from, .slide-fade-leave-to {
  opacity: 0;
  transform: translateY(20px);
}

.project-link {
  text-decoration: none;
  color: inherit;
  display: inline-block;
}

.project-link h3 {
  display: flex;
  align-items: center;
  gap: 10px; 
  transition: color 0.3s ease;
}

.project-link:hover h3 {
  color: #00b32d;
}

.project-link svg {
  width: 20px;
  height: 20px;
}
</style>